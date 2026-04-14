# Order Book

Đọc depth, best price, và spread thời gian thực từ Exchange contract.

## View functions

### getBestPrices

Trả về bid/ask sát nhất cho cả YES và NO:

```typescript
const exchange = new ethers.Contract(EXCHANGE, [
  "function getBestPrices(bytes32) view returns (uint256,uint256,uint256,uint256)"
], provider);

const [bestBidYes, bestAskYes, bestBidNo, bestAskNo] = await exchange.getBestPrices(marketId);

console.log(`YES: ${bestBidYes} bid / ${bestAskYes} ask`);
console.log(`NO:  ${bestBidNo} bid / ${bestAskNo} ask`);
```

### getOrderBook

Trả về độ sâu đầy đủ đến `depth` mức giá:

```typescript
const exchange = new ethers.Contract(EXCHANGE, [
  "function getOrderBook(bytes32,uint8) view returns (uint256[],uint256[],uint256[],uint256[])"
], provider);

const [yesBids, yesAsks, noBids, noAsks] = await exchange.getOrderBook(marketId, 10);
```

### getDepthAtPrice

Trả về tổng số có sẵn tại một mức giá:

```typescript
const exchange = new ethers.Contract(EXCHANGE, [
  "function getDepthAtPrice(bytes32,uint8,uint256) view returns (uint256)"
], provider);

// Depth cho BUY_YES ở $0.55
const depth = await exchange.getDepthAtPrice(marketId, 0, 550000);
console.log(`${ethers.formatUnits(depth, 6)} token sẵn có ở $0.55`);
```

## Tính spread

```
Spread = Best Ask − Best Bid
Spread % = Spread / Midpoint × 100
Midpoint = (Best Bid + Best Ask) / 2
```

Spread hẹp cho thấy thanh khoản dày. Spread > $0.05 nghĩa là sổ lệnh mỏng — nên cân nhắc AMM.

## Tiếp theo

- [Smart Routing](smart-routing.md) — cách Router dùng dữ liệu order book
- [Market Making](../liquidity/market-making.md) — cung cấp thanh khoản CLOB
- [Developer: Tích hợp giao dịch](../developers/trading-integration.md) — hướng dẫn đầy đủ
