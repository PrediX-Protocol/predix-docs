# Order Book

Read real-time CLOB depth, best prices, and spread from the Exchange contract.

## View Functions

### getBestPrices

Returns the tightest bid/ask for both YES and NO:

```typescript
const exchange = new ethers.Contract(EXCHANGE, [
  "function getBestPrices(bytes32) view returns (uint256,uint256,uint256,uint256)"
], provider);

const [bestBidYes, bestAskYes, bestBidNo, bestAskNo] = await exchange.getBestPrices(marketId);

console.log(`YES: ${bestBidYes} bid / ${bestAskYes} ask`);
console.log(`NO:  ${bestBidNo} bid / ${bestAskNo} ask`);
console.log(`Spread: ${Number(bestAskYes) - Number(bestBidYes)} (${((Number(bestAskYes) - Number(bestBidYes)) / 10000).toFixed(2)}%)`);
```

### getOrderBook

Returns full depth up to `depth` price levels:

```typescript
const exchange = new ethers.Contract(EXCHANGE, [
  "function getOrderBook(bytes32,uint8) view returns (uint256[],uint256[],uint256[],uint256[])"
], provider);

const [yesBids, yesAsks, noBids, noAsks] = await exchange.getOrderBook(marketId, 10);
```

### getDepthAtPrice

Returns total amount available at a specific price level:

```typescript
const exchange = new ethers.Contract(EXCHANGE, [
  "function getDepthAtPrice(bytes32,uint8,uint256) view returns (uint256)"
], provider);

// Depth for BUY_YES at $0.55
const depth = await exchange.getDepthAtPrice(marketId, 0, 550000);
console.log(`${ethers.formatUnits(depth, 6)} tokens available at $0.55`);
```

## Spread Calculation

```
Spread = Best Ask − Best Bid
Spread % = Spread / Midpoint × 100
Midpoint = (Best Bid + Best Ask) / 2
```

Tight spreads indicate deep liquidity. Spread > $0.05 suggests thin order book — consider AMM for execution.

## Next Steps

- [Smart Routing](smart-routing.md) — how Router uses order book data
- [Liquidity: Market Making](../liquidity/market-making.md) — providing CLOB liquidity
- [Developer: Trading Integration](../developers/trading-integration.md) — full code guide
