---
description: Error handling patterns — decode custom errors, map tới UX actions, retry strategies.
---

# Error Handling

PrediX contracts dùng custom errors (gas-efficient, typed). FE/bot decode + switch exhaustive để cho UX đúng.

## Custom error parsing

### viem pattern

```typescript
import { decodeErrorResult, BaseError, ContractFunctionRevertedError } from 'viem';

async function tradeWithErrorHandling(args) {
  try {
    await walletClient.writeContract({ /* ... */ });
  } catch (err) {
    if (err instanceof BaseError) {
      const revertError = err.walk(e => e instanceof ContractFunctionRevertedError);
      if (revertError instanceof ContractFunctionRevertedError) {
        const errorName = revertError.data?.errorName;
        const args = revertError.data?.args;
        return handleCustomError(errorName, args);
      }
    }
    throw err;
  }
}

function handleCustomError(name: string | undefined, args: any) {
  switch (name) {
    case 'Router_SlippageExceeded':
      return { userMessage: 'Giá đã thay đổi, thử lại với quote mới', retry: true };
    case 'Router_DeadlineExpired':
      return { userMessage: 'Giao dịch quá chậm, thử lại ngay', retry: true };
    case 'Market_Ended':
      return { userMessage: 'Market đã kết thúc', retry: false };
    case 'Market_OracleNotApproved':
      return { userMessage: 'Oracle bị revoke — liên hệ team', retry: false };
    case 'Exchange_InvalidPrice':
      return { userMessage: 'Giá không hợp lệ ($0.01-$0.99, step $0.01)', retry: false };
    case 'Exchange_MaxOrdersExceeded':
      return { userMessage: 'Đã đạt 50 lệnh pending, huỷ lệnh cũ trước', retry: false };
    case 'Hook_IdentityCommitMissing':
      return { userMessage: 'Trade phải đi qua Router, không gọi thẳng pool', retry: false };
    default:
      return { userMessage: 'Lỗi không xác định', retry: false };
  }
}
```

### Manual decode

Nếu không dùng viem wrap:

```typescript
import { decodeErrorResult } from 'viem';

try {
  await publicClient.simulateContract({ /* ... */ });
} catch (err: any) {
  const data = err.cause?.data ?? err.data;
  if (!data || data === '0x') throw err;

  // Try decode với từng ABI
  for (const abi of [RouterAbi, ExchangeAbi, MarketFacetAbi, HookAbi]) {
    try {
      const decoded = decodeErrorResult({ abi, data });
      console.log(`Revert: ${decoded.errorName}`, decoded.args);
      return decoded;
    } catch {
      // Not this ABI
    }
  }
  throw err;  // Unknown
}
```

## Error categories

Xem [Custom Errors Registry](../smart-contracts/custom-errors.md) cho full list. Tóm tắt:

### User input errors (retryable với adjust)

| Error | Từ contract | Fix user action |
|---|---|---|
| `Router_SlippageExceeded(expected, actual)` | Router | Increase slippage tolerance |
| `Router_DeadlineExpired(deadline, now)` | Router | Retry với deadline xa hơn |
| `Exchange_InvalidPrice(price)` | Exchange | Chỉnh price theo $0.01 step |
| `Exchange_OrderTooSmall(amount)` | Exchange | Tăng amount ≥ $1 |
| `Market_RedemptionFeeTooHigh` | Market (admin) | Giảm fee ≤ 15% |

### State errors (không retryable)

| Error | Meaning |
|---|---|
| `Market_NotFound` | marketId không tồn tại |
| `Market_Ended` | Market đã qua endTime |
| `Market_Resolved` | Market đã resolve |
| `Market_NotResolved` | Redeem trước resolve |
| `Market_RefundModeActive` | Split/merge trong refund mode |
| `Market_OracleNotApproved` | Oracle bị revoke (FINAL-D-03) |
| `Exchange_MaxOrdersExceeded` | Đạt 50 orders pending |
| `Pausable_Paused(module)` | Module đang pause |

### Access errors

| Error | Meaning |
|---|---|
| `AC_Unauthorized(role, caller)` | Thiếu role |
| `AC_LastAdminProtection` | Cannot revoke last admin |

### Security errors (shouldn't happen in normal flow)

| Error | Meaning | Significance |
|---|---|---|
| `Router_NonZeroBalance` | Router post-call balance ≠ 0 | INV-3 violation — **critical bug, report** |
| `Hook_IdentityCommitMissing` | Swap thiếu identity commit | Likely user calling pool directly, should go qua Router |
| `Hook_UntrustedRouter` | Router không trong whitelist | Config issue |

## Retry strategies

### Exponential backoff

```typescript
async function retryWithBackoff<T>(
  fn: () => Promise<T>,
  options: { maxAttempts?: number; baseMs?: number } = {}
): Promise<T> {
  const { maxAttempts = 3, baseMs = 1000 } = options;
  for (let attempt = 1; attempt <= maxAttempts; attempt++) {
    try {
      return await fn();
    } catch (err) {
      if (attempt === maxAttempts) throw err;
      const delayMs = baseMs * Math.pow(2, attempt - 1);
      await new Promise(r => setTimeout(r, delayMs));
    }
  }
  throw new Error('Unreachable');
}
```

### Retry khi slippage

```typescript
async function buyYesWithRetry(marketId: bigint, usdcIn: bigint, slippageBps = 200) {
  for (let attempt = 0; attempt < 3; attempt++) {
    try {
      const quote = await quoteBuyYes(marketId, usdcIn);
      const minOut = (quote * (10000n - BigInt(slippageBps))) / 10000n;
      return await submitBuyYes(marketId, usdcIn, minOut);
    } catch (err) {
      const decoded = tryDecodeError(err);
      if (decoded?.errorName === 'Router_SlippageExceeded') {
        slippageBps += 100;  // tăng 1% mỗi lần
        continue;
      }
      throw err;
    }
  }
  throw new Error('Slippage retry exhausted');
}
```

### Retry khi RPC flaky

```typescript
// viem built-in retry qua transport config
import { http } from 'viem';

const transport = http('https://sepolia.unichain.org', {
  retryCount: 3,
  retryDelay: 500,
});
```

## Backend API errors

BE errors khác SC errors — dùng error code enum:

| HTTP | Code | Fix |
|---|---|---|
| 400 | `invalid_request` | Fix input |
| 401 | `auth_required` | Re-prompt SIWE sign-in |
| 403 | `forbidden` | Role/ownership check |
| 404 | `not_found` / `market_not_found` | Resource không tồn tại |
| 429 | `rate_limit_exceeded` | Exponential backoff |
| 500 | `internal_error` | Report với X-Request-Id |
| 503 | `indexer_unavailable` | Retry sau vài giây |

Xem [Backend Error Codes](../backend-api/09-error-codes.md) cho full list.

```typescript
async function callBackend<T>(path: string): Promise<T> {
  const res = await fetch(path);
  if (!res.ok) {
    const body = await res.json();
    switch (body.error.code) {
      case 'auth_required':
        window.dispatchEvent(new Event('predix:session-expired'));
        throw new Error('Auth required');
      case 'rate_limit_exceeded':
        const retryAfter = res.headers.get('Retry-After');
        await new Promise(r => setTimeout(r, Number(retryAfter) * 1000));
        return callBackend(path);  // retry after
      default:
        throw new Error(`${body.error.code}: ${body.error.message}`);
    }
  }
  return (await res.json()).data;
}
```

## Debugging tips

1. **Log `X-Request-Id`** khi báo bug BE — team lookup trace
2. **Copy revert data** `0x...` khi báo bug SC — decode được
3. **Test trên tenderly** — [tenderly.co](https://tenderly.co) cho detailed revert trace
4. **Fork test locally** — `forge test --fork-url https://sepolia.unichain.org`
5. **Simulate trước submit** — `publicClient.simulateContract` reveal revert trước khi spend gas

Xem full error registry: [Custom Errors](../smart-contracts/custom-errors.md).
