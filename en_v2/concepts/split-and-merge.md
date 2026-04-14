# Split & Merge

Split and merge are the fundamental mechanisms that create and destroy outcome tokens, anchoring prices to the $1 invariant.

## Split: USDC → YES + NO

Deposit USDC into the Diamond contract to receive equal amounts of YES and NO tokens.

```
$100 USDC → 100 YES + 100 NO
```

## Merge: YES + NO → USDC

Return equal amounts of YES and NO tokens to receive USDC back.

```
100 YES + 100 NO → $100 USDC
```

## Why It Matters

1. **Price anchor:** Ensures YES + NO ≈ $1.00 via arbitrage
2. **Liquidity creation:** Anyone can create tokens without a counterparty
3. **Market efficiency:** Mispricing is corrected instantly by arbitrageurs

### Arbitrage Example

YES = $0.55, NO = $0.40 → Total = $0.95 (below $1)

1. Split $100 USDC → 100 YES + 100 NO
2. Sell 100 YES at $0.55 → $55
3. Sell 100 NO at $0.40 → $40
4. Total received: $95... wait, that's a loss.

Actually: if YES = $0.55, NO = $0.40 → total $0.95. Arbitrageur **buys** both (total cost $0.95), **merges** → gets $1.00 USDC. Profit: $0.05/pair.

## Code Example

```typescript
import { ethers } from "ethers";

const DIAMOND = "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A";
const USDC = "0x12fd156C8b5F2901BA2781d97db84AaC56b2b911";

// Approve USDC to Diamond
const usdc = new ethers.Contract(USDC, ["function approve(address,uint256)"], signer);
await usdc.approve(DIAMOND, ethers.parseUnits("100", 6));

const diamond = new ethers.Contract(DIAMOND, [
  "function splitPosition(bytes32,uint256)",
  "function mergePositions(bytes32,uint256)"
], signer);

// Split: 100 USDC → 100 YES + 100 NO
await diamond.splitPosition(marketId, ethers.parseUnits("100", 6));

// Merge: 100 YES + 100 NO → 100 USDC
// (requires approving both YES and NO tokens to Diamond first)
await diamond.mergePositions(marketId, ethers.parseUnits("100", 6));
```

> ⚠️ To merge, you must approve **both** YES and NO tokens to the Diamond contract before calling `mergePositions`.

## Group Operations

For [multi-outcome markets](multi-outcome-markets.md), use `groupSplit` and `groupMerge` to operate across all markets in a category simultaneously.

## Next Steps

- [Resolution](resolution.md) — what happens when the market ends
- [Fees](fees.md) — fee schedule for AMM and CLOB
- [Trading Overview](../trading/overview.md) — buying and selling tokens
