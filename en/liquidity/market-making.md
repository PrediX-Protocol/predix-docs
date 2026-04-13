---
description: CLOB market making — providing order book liquidity
---

# Market Making

## Overview

Market making on the CLOB involves placing simultaneous buy and sell orders to capture the spread.

## Basic Strategy

```typescript
const exchange = new ethers.Contract(EXCHANGE_ADDRESS, EXCHANGE_ABI, signer);

// Place bid (BUY_YES) and ask (SELL_YES)
const bidPrice = ethers.parseUnits("0.48", 6);
const askPrice = ethers.parseUnits("0.52", 6);
const amount = ethers.parseUnits("100", 6);

// BUY_YES: deposit USDC
await exchange.placeOrder(marketId, 0, bidPrice, amount); // side 0 = BUY_YES

// SELL_YES: deposit YES tokens (must hold from split)
await exchange.placeOrder(marketId, 1, askPrice, amount); // side 1 = SELL_YES
```

## Spread Management

| Market Phase | Recommended Spread |
| ------------ | ------------------ |
| New market (low volume) | $0.05–$0.10 |
| Active market | $0.02–$0.05 |
| Near expiry (< 24h) | $0.05–$0.10 or wider |

## Inventory Risk

When you get filled on one side more than the other, you accumulate directional exposure:

- Too many YES tokens → your position profits if YES wins
- Too many NO tokens → your position profits if NO wins

Manage by adjusting prices or using split/merge to rebalance.

## Market Maker Bot

PrediX provides a reference Market Maker Bot implementation. See the GitHub repository for details.

---

**Next**: [Liquidity Overview](overview.md) · [Limit Orders](../trading/limit-orders.md)
