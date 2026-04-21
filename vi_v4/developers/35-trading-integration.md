---
description: Full trading integration — market orders (Router), limit orders (Exchange), Permit2.
---

# Trading Integration

## Approve USDC

Trước khi trade, approve USDC cho contract tương ứng. Cho integrator lâu dài, dùng `MaxUint256` 1 lần:

```typescript
import { maxUint256 } from 'viem';

// Router — cho market orders (buyYes/sellYes/buyNo/sellNo)
await walletClient.writeContract({
  address: USDC, abi: ERC20Abi,
  functionName: 'approve', args: [ROUTER, maxUint256],
});

// Exchange — cho limit orders (placeOrder side=BUY_*)
await walletClient.writeContract({
  address: USDC, abi: ERC20Abi,
  functionName: 'approve', args: [EXCHANGE, maxUint256],
});

// Diamond — cho split/merge
await walletClient.writeContract({
  address: USDC, abi: ERC20Abi,
  functionName: 'approve', args: [DIAMOND, maxUint256],
});
```

Alternative: **Permit2** — 1 signature thay vì tx. Xem section "Permit2" bên dưới.

## Market Order Flow

### Buy YES

```typescript
import { parseUnits } from 'viem';

const marketId = 42n;
const usdcIn = parseUnits('10', 6);  // 10 USDC

// 1. Quote (revert-and-decode pattern)
const expectedYes = await quoteBuyYes(marketId, usdcIn);

// 2. Apply slippage (2%)
const minYesOut = (expectedYes * 98n) / 100n;

// 3. Set deadline (5 min)
const deadline = BigInt(Math.floor(Date.now() / 1000) + 300);

// 4. Submit
const hash = await walletClient.writeContract({
  address: ROUTER, abi: RouterAbi,
  functionName: 'buyYes',
  args: [marketId, usdcIn, minYesOut, account.address, deadline],
});

const receipt = await publicClient.waitForTransactionReceipt({ hash });

// 5. Parse Trade event cho actual fill
const tradeLog = receipt.logs.find(log => log.address === ROUTER);
// decode với viem decodeEventLog
```

### Sell YES

```typescript
const yesIn = parseUnits('50', 6);  // 50 YES tokens

// Approve YES → Router
await walletClient.writeContract({
  address: market.yesToken, abi: ERC20Abi,
  functionName: 'approve', args: [ROUTER, yesIn],
});

const expectedUsdc = await quoteSellYes(marketId, yesIn);
const minUsdcOut = (expectedUsdc * 98n) / 100n;

await walletClient.writeContract({
  address: ROUTER, abi: RouterAbi,
  functionName: 'sellYes',
  args: [marketId, yesIn, minUsdcOut, account.address, deadline],
});
```

### Buy / Sell NO (virtual)

```typescript
await walletClient.writeContract({
  address: ROUTER, abi: RouterAbi,
  functionName: 'buyNo',
  args: [marketId, usdcIn, mintAmount, minNoOut, account.address, deadline],
});
```

`mintAmount` là hint cho Diamond mint size (simulate cho efficiency). Quote function return cả 2 giá trị.

---

## Quote decode helper

Router v4-quoter pattern: `quoteBuyYes` simulate rồi `revert(abi.encode(quoteResult))`. Decode:

```typescript
import { decodeErrorResult } from 'viem';

async function quoteBuyYes(marketId: bigint, usdcIn: bigint): Promise<bigint> {
  try {
    await publicClient.simulateContract({
      address: ROUTER, abi: RouterAbi,
      functionName: 'quoteBuyYes',
      args: [marketId, usdcIn],
    });
    throw new Error('Quote should have reverted');
  } catch (err: any) {
    // viem wraps revert; raw revert data in err.cause.data hoặc err.data
    const data = err.cause?.data ?? err.data;
    if (!data) throw err;

    // Router encodes quote as bytes: abi.encode(uint256)
    // Strip selector (first 4 bytes = 0x + 8 hex chars)
    const encoded = `0x${data.slice(10)}`;
    const [quoteResult] = decodeAbiParameters(
      [{ type: 'uint256', name: 'yesOut' }],
      encoded,
    );
    return quoteResult;
  }
}
```

Alternative: call `quoteBuyYes` qua viem `call()` và catch revert:

```typescript
const { data: revertData } = await publicClient.call({
  to: ROUTER,
  data: encodeFunctionData({
    abi: RouterAbi,
    functionName: 'quoteBuyYes',
    args: [marketId, usdcIn],
  }),
});
// revertData chứa encoded quote
```

---

## Limit Order Flow

### Place Limit Order

```typescript
// Approve USDC (cho buy) hoặc YES/NO token (cho sell) tới Exchange
await walletClient.writeContract({
  address: USDC, abi: ERC20Abi,
  functionName: 'approve', args: [EXCHANGE, maxUint256],
});

// Side enum: 0=BUY_YES, 1=SELL_YES, 2=BUY_NO, 3=SELL_NO
const side = 0;  // BUY_YES
const price = parseUnits('0.55', 6);  // $0.55 (must be step $0.01, 550000)
const amount = parseUnits('100', 6);  // 100 tokens

const hash = await walletClient.writeContract({
  address: EXCHANGE, abi: ExchangeAbi,
  functionName: 'placeOrder',
  args: [marketId, side, price, amount],
});

const receipt = await publicClient.waitForTransactionReceipt({ hash });

// Parse OrderPlaced event để lấy orderId
const orderPlacedLog = receipt.logs.find(/* match OrderPlaced */);
// orderId = log.args.orderId
```

### Cancel Order

```typescript
await walletClient.writeContract({
  address: EXCHANGE, abi: ExchangeAbi,
  functionName: 'cancelOrder',
  args: [orderId],
});
```

- Trước khi market kết thúc: chỉ owner cancel được
- Sau khi market kết thúc: anyone cancel được (unlock collateral cho owner)

### Read Order Book

```typescript
const [yesBids, yesAsks, noBids, noAsks] = await publicClient.readContract({
  address: EXCHANGE, abi: ExchangeAbi,
  functionName: 'getOrderBook',
  args: [marketId, 20],  // top 20 levels
});
```

### Best Prices

```typescript
const [bestBidYes, bestAskYes, bestBidNo, bestAskNo] = await publicClient.readContract({
  address: EXCHANGE, abi: ExchangeAbi,
  functionName: 'getBestPrices',
  args: [marketId],
});
```

---

## Permit2 Integration

Permit2 cho phép 1 signature thay vì approve tx. UX tốt hơn + tiết kiệm gas.

### Setup

User ký signature off-chain, submit along với trade call:

```typescript
import { signPermit2Transfer } from '@uniswap/permit2-sdk';

const PERMIT2 = '0x000000000022D473030F116dDEE9F6B43aC78BA3';

const permit = {
  permitted: { token: USDC, amount: usdcIn },
  spender: ROUTER,
  nonce: await getPermit2Nonce(account.address),
  deadline,
};

const signature = await walletClient.signTypedData({
  domain: { name: 'Permit2', chainId: 1301, verifyingContract: PERMIT2 },
  types: { /* Permit2 EIP-712 types */ },
  primaryType: 'PermitTransferFrom',
  message: permit,
});
```

Sau đó submit qua Router Permit2 variant:

```typescript
await walletClient.writeContract({
  address: ROUTER, abi: RouterAbi,
  functionName: 'buyYesWithPermit2',
  args: [marketId, usdcIn, minOut, recipient, deadline, permit, signature],
});
```

Reference: [Uniswap Permit2 docs](https://docs.uniswap.org/contracts/permit2/overview).

---

## Full example — Market order với error handling

```typescript
async function buyYesWithHandling(marketId: bigint, usdcAmount: string) {
  try {
    // 1. Quote
    const expected = await quoteBuyYes(marketId, parseUnits(usdcAmount, 6));

    // 2. Slippage 2%
    const minOut = (expected * 98n) / 100n;
    const deadline = BigInt(Math.floor(Date.now() / 1000) + 300);

    // 3. Submit
    const hash = await walletClient.writeContract({
      address: ROUTER, abi: RouterAbi,
      functionName: 'buyYes',
      args: [marketId, parseUnits(usdcAmount, 6), minOut, account.address, deadline],
    });

    const receipt = await publicClient.waitForTransactionReceipt({ hash });

    if (receipt.status === 'reverted') {
      throw new Error('Tx reverted on chain');
    }

    return receipt;
  } catch (err) {
    // Parse custom error
    const parsed = parseCustomError(err);
    if (parsed?.errorName === 'Router_SlippageExceeded') {
      console.log('Slippage exceeded — retry với quote mới');
    } else if (parsed?.errorName === 'Router_DeadlineExpired') {
      console.log('Deadline quá sát — dùng longer window');
    } else if (parsed?.errorName === 'Market_Ended') {
      console.log('Market đã kết thúc');
    } else {
      console.error('Unknown error:', err);
    }
    throw err;
  }
}
```

Xem [Error Handling](38-common-errors.md) cho `parseCustomError` implementation.

---

## Advanced: Direct Exchange fill (taker path)

Bot/aggregator có thể gọi thẳng Exchange taker path thay vì qua Router:

```typescript
const maxPrice = parseUnits('0.55', 6);
const [filled, totalCost] = await walletClient.writeContract({
  address: EXCHANGE, abi: ExchangeAbi,
  functionName: 'fillAsChain',
  args: [marketId, BUY_YES, maxPrice, amount, taker, recipient, 20],
});
```

Ưu điểm: skip AMM, chắc chắn CLOB. Nhược điểm: miss AMM liquidity, cần tự handle slippage/deadline.

Hầu hết integrator nên dùng Router để hưởng lợi từ smart routing.
