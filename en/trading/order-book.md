---
description: Query the on-chain order book — prices, depth, and spreads
---

# Order Book

## View Functions

### Best Prices

```typescript
const [bestBidYes, bestAskYes, bestBidNo, bestAskNo] =
  await exchange.getBestPrices(marketId);

console.log("YES Bid:", ethers.formatUnits(bestBidYes, 6));
console.log("YES Ask:", ethers.formatUnits(bestAskYes, 6));
console.log("Spread:", ethers.formatUnits(bestAskYes - bestBidYes, 6));
```

### Full Order Book

```typescript
// depth = number of price levels to return
const depth = 10;
const [yesBids, yesAsks, noBids, noAsks] =
  await exchange.getOrderBook(marketId, depth);
```

### Depth at Specific Price

```typescript
// Side: 0=BUY_YES, 1=SELL_YES, 2=BUY_NO, 3=SELL_NO
const price = ethers.parseUnits("0.65", 6);
const amount = await exchange.getDepthAtPrice(marketId, 0, price);
console.log("BUY_YES depth at $0.65:", ethers.formatUnits(amount, 6));
```

### Get Single Order

```typescript
const order = await exchange.getOrder(orderId);
// Returns: owner, marketId, side, price, amount (remaining), timestamp
```

## Spread Calculation

```typescript
const [bestBid, bestAsk] = [bestBidYes, bestAskYes];
const midPrice = (bestBid + bestAsk) / 2n;
const spreadBps = ((bestAsk - bestBid) * 10000n) / midPrice;
console.log("Spread (bps):", spreadBps.toString());
```

---

**Next**: [Virtual NO Pricing](virtual-no-pricing.md) · [Limit Orders](limit-orders.md)
