---
description: Lifecycle operations — split USDC → pair, merge pair → USDC, resolve, redeem, refund.
---

# Split, Merge, Resolve & Redeem

## Split Position

```typescript
import { parseUnits } from 'viem';

// Approve USDC tới Diamond
await walletClient.writeContract({
  address: USDC, abi: ERC20Abi,
  functionName: 'approve', args: [DIAMOND, parseUnits('100', 6)],
});

// Split: 100 USDC → 100 YES + 100 NO
const hash = await walletClient.writeContract({
  address: DIAMOND, abi: MarketFacetAbi,
  functionName: 'splitPosition',
  args: [marketId, parseUnits('100', 6)],
});

await publicClient.waitForTransactionReceipt({ hash });
```

**Before**: user có 100 USDC, 0 YES, 0 NO
**After**: user có 0 USDC, 100 YES, 100 NO
**Market state**: `totalCollateral += 100_000_000`, `YES.totalSupply += 100_000_000`, `NO.totalSupply += 100_000_000` (INV-1)

### Use cases

- **Market maker prep**: split để có inventory YES + NO cho posting limit orders
- **Arb**: split $1 rồi bán cả 2 side nếu giá tổng > $1
- **Hedging**: split rồi giữ NO, bán YES qua AMM → effectively bought NO

## Merge Positions

```typescript
// Approve YES + NO tokens tới Diamond
await walletClient.writeContract({
  address: market.yesToken, abi: ERC20Abi,
  functionName: 'approve', args: [DIAMOND, parseUnits('50', 6)],
});

await walletClient.writeContract({
  address: market.noToken, abi: ERC20Abi,
  functionName: 'approve', args: [DIAMOND, parseUnits('50', 6)],
});

// Merge: 50 YES + 50 NO → 50 USDC
await walletClient.writeContract({
  address: DIAMOND, abi: MarketFacetAbi,
  functionName: 'mergePositions',
  args: [marketId, parseUnits('50', 6)],
});
```

Burn cặp, return USDC 1:1. Use case: exit position cleanly trước khi market kết thúc.

## Resolve Market

Khi `endTime` đã qua + oracle có kết quả:

```typescript
// Anyone có thể trigger resolve
const hash = await walletClient.writeContract({
  address: DIAMOND, abi: MarketFacetAbi,
  functionName: 'resolveMarket',
  args: [marketId],
});
```

Flow nội bộ:

1. Diamond check oracle vẫn approved (FINAL-D-03 re-verify)
2. Diamond call `oracle.getResolution(marketId)`
3. Nếu resolved → set `market.isResolved = true`, `outcome`, `resolvedAt`
4. Emit `MarketResolved(marketId, outcome)`

Sau bước này, winning token holders có thể redeem.

## Redeem Tokens

```typescript
// Redeem all winning tokens
const hash = await walletClient.writeContract({
  address: DIAMOND, abi: MarketFacetAbi,
  functionName: 'redeemMarketTokens',
  args: [marketId],
});

// Hoặc redeem partial
await walletClient.writeContract({
  address: DIAMOND, abi: MarketFacetAbi,
  functionName: 'redeem',
  args: [marketId, parseUnits('50', 6)],
});
```

Contract:

1. Check `market.isResolved`
2. Determine winning side (`outcome`) → user's winning token balance
3. Calculate `fee = amount × snapshottedRedemptionFeeBps / 10000`
4. Burn winning tokens
5. Transfer `amount - fee` USDC → user
6. Transfer `fee` USDC → fee recipient

Emit `TokensRedeemed(marketId, user, amount, payout)`.

**Partial redeem** (`redeem`) giống `redeemMarketTokens` nhưng specify amount.

## Refund (refund mode)

Khi market không thể resolve công bằng, operator call `enableRefundMode`:

```typescript
// Operator
await walletClient.writeContract({
  address: DIAMOND, abi: MarketFacetAbi,
  functionName: 'enableRefundMode',
  args: [marketId],
});
```

User sau đó burn token nhận USDC:

```typescript
// Approve tokens tới Diamond
await walletClient.writeContract({
  address: market.yesToken, abi: ERC20Abi,
  functionName: 'approve', args: [DIAMOND, yesBalance],
});

await walletClient.writeContract({
  address: DIAMOND, abi: MarketFacetAbi,
  functionName: 'refund',
  args: [marketId, yesBalance],
});
```

Return USDC 1:1 với token amount (pro-rata nếu collateral không đủ do dust).

**Refund mode is one-way** — một khi active, không off được. Guarantees user có exit path.

## Emergency Resolve

Nếu oracle stuck không resolve được sau 7 ngày:

```typescript
// OPERATOR_ROLE
await walletClient.writeContract({
  address: DIAMOND, abi: MarketFacetAbi,
  functionName: 'emergencyResolve',
  args: [marketId, trueIfYesWins],
});
```

Check: `block.timestamp >= endTime + 7 days`.

## Read position state

```typescript
// Check user's YES balance
const yesBalance = await publicClient.readContract({
  address: market.yesToken, abi: ERC20Abi,
  functionName: 'balanceOf', args: [account.address],
});

// Check market resolution status
const market = await publicClient.readContract({
  address: DIAMOND, abi: MarketFacetAbi,
  functionName: 'getMarket', args: [marketId],
});

if (market.isResolved) {
  console.log('Winning outcome:', market.outcome ? 'YES' : 'NO');
  const winningToken = market.outcome ? market.yesToken : market.noToken;
  const myWinningBalance = await publicClient.readContract({
    address: winningToken, abi: ERC20Abi,
    functionName: 'balanceOf', args: [account.address],
  });
  console.log('Can redeem:', formatUnits(myWinningBalance, 6));
}
```

Alternative: dùng BE `/api/v2/users/:address/portfolio` cho aggregated view.

## Error cases

| Error | Scenario | Fix |
|---|---|---|
| `Market_Resolved` | Split sau resolve | Không làm được — đã closed |
| `Market_Ended` | Split sau endTime | Không làm được |
| `Market_NotResolved` | Redeem trước resolve | Đợi `resolveMarket` |
| `Market_RefundModeActive` | Split trong refund mode | Dùng `refund` thay thế |
| `Market_RefundModeNotActive` | `refund` khi chưa enable | Không applicable |
| `Market_EmergencyDelayNotMet` | `emergencyResolve` quá sớm | Chờ `endTime + 7d` |
| `Market_InsufficientBalance` | Redeem > balance | Check balance trước |

Xem [Error Handling](38-common-errors.md) cho full list.

## Batch operations (advanced)

Không có built-in multicall trên Diamond (hiện tại). Option:

- Dùng 3rd-party Multicall3: `0xcA11bde05977b3631167028862bE2a173976CA11` (deployed cross-chain)
- Hoặc gọi sequential trong FE (parallel Promise.all chấp nhận N txs)

Ví dụ split + approve YES + cancel cùng market → 3 separate txs.
