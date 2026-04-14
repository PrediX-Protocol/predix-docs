# Router Contract

The Router is the primary trading interface, aggregating liquidity from both CLOB and AMM for optimal execution.

## Trading Functions (PUBLIC)

```solidity
buyYes(bytes32 marketId, uint256 usdcIn, uint256 minYesOut, address recipient, uint256 deadline) → uint256 yesOut
sellYes(bytes32 marketId, uint256 yesIn, uint256 minUsdcOut, address recipient, uint256 deadline) → uint256 usdcOut
buyNo(bytes32 marketId, uint256 usdcIn, uint256 mintAmount, uint256 minNoOut, address recipient, uint256 deadline) → uint256 noOut
sellNo(bytes32 marketId, uint256 noIn, uint256 minUsdcOut, address recipient, uint256 deadline) → uint256 usdcOut
```

## Quote Functions (VIEW)

```solidity
quoteBuyYes(bytes32 marketId, uint256 usdcIn) → uint256
quoteSellYes(bytes32 marketId, uint256 yesIn) → uint256
quoteBuyNo(bytes32 marketId, uint256 usdcIn) → uint256
quoteSellNo(bytes32 marketId, uint256 noIn) → uint256
```

## Routing Logic

```
1. Receive user's USDC/tokens
2. Check CLOB depth at current best price
3. Fill from CLOB up to available depth
4. Route remaining amount to AMM via Hook
5. Verify output ≥ minOut (revert if not)
6. Transfer output tokens to recipient
```

## Flash Accounting (Uniswap v4)

The Router uses Uniswap v4's `unlock` callback pattern for atomic multi-step operations:

- **buyNo:** Unlocks Pool Manager → splits USDC → swaps YES for USDC on AMM → returns NO to user
- **sellNo:** Unlocks Pool Manager → swaps USDC for YES on AMM → merges YES + NO → returns USDC

All steps execute atomically. If any step fails, the entire transaction reverts.

## Virtual NO Pricing

NO tokens don't have a direct pool. The Router synthesizes NO trades:

- **Buy NO = Split + Sell YES:** `USDC → split → (YES + NO) → sell YES → keep NO`
- **Sell NO = Buy YES + Merge:** `NO + (buy YES) → merge → USDC`

User sees a simple `buyNo(marketId, usdcIn, ...)` call. The Router handles complexity internally.

## Next Steps

- [Hook](hook.md) — AMM implementation details
- [Trading: Smart Routing](../trading/smart-routing.md) — routing examples
- [Trading: Market Orders](../trading/market-orders.md) — code examples
