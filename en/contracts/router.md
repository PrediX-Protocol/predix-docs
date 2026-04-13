---
description: Smart Router — hybrid CLOB + AMM execution via Uniswap v4
---

# Router Contract

The PrediXRouter aggregates liquidity from the CLOB (Exchange) and AMM (Uniswap v4) to provide best execution for traders.

## Trading Functions

```solidity
buyYes(bytes32 marketId, uint256 usdcIn, uint256 minYesOut,
       address recipient, uint256 deadline) → uint256 yesOut

sellYes(bytes32 marketId, uint256 yesIn, uint256 minUsdcOut,
        address recipient, uint256 deadline) → uint256 usdcOut

buyNo(bytes32 marketId, uint256 usdcIn, uint256 mintAmount,
      uint256 minNoOut, address recipient, uint256 deadline) → uint256 noOut

sellNo(bytes32 marketId, uint256 noIn, uint256 minUsdcOut,
       address recipient, uint256 deadline) → uint256 usdcOut
```

## Quote Functions

```solidity
quoteBuyYes(bytes32 marketId, uint256 usdcIn) → uint256
quoteSellYes(bytes32 marketId, uint256 yesIn) → uint256
quoteBuyNo(bytes32 marketId, uint256 usdcIn) → uint256
quoteSellNo(bytes32 marketId, uint256 noIn) → uint256
```

## Routing Logic

```
buyYes($100):
  1. Check CLOB for SELL_YES orders better than AMM price
  2. Fill CLOB (zero slippage, limit price)
  3. Route remaining to Uniswap v4 AMM swap
  4. Return total YES to recipient
```

## Virtual NO Pricing

Since only YES/USDC pools exist:

- **Buy NO**: Split USDC → YES+NO → sell YES via AMM → keep NO
- **Sell NO**: Buy YES from AMM → merge YES+NO → return USDC

## Flash Accounting

The Router uses Uniswap v4's flash accounting (unlock callback pattern) for gas-efficient multi-step operations.

> ⚠️ **Parameters**: Always set `deadline` (typically 5 minutes) and `minOut` (slippage protection). Transactions revert if conditions aren't met.

---

**Next**: [Hook](hook.md) · [Smart Routing](../trading/smart-routing.md) · [Market Orders](../trading/market-orders.md)
