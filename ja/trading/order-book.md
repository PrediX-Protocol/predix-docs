---
description: オンチェーンオーダーブックの照会 — 価格、板の厚み、スプレッド
---

# オーダーブック

## 参照関数

### 最良価格

```typescript
const [bestBidYes, bestAskYes, bestBidNo, bestAskNo] =
  await exchange.getBestPrices(marketId);

console.log("YES買い気配:", ethers.formatUnits(bestBidYes, 6));
console.log("YES売り気配:", ethers.formatUnits(bestAskYes, 6));
console.log("スプレッド:", ethers.formatUnits(bestAskYes - bestBidYes, 6));
```

### 全オーダーブック

```typescript
// depth = 返す価格レベルの数
const depth = 10;
const [yesBids, yesAsks, noBids, noAsks] =
  await exchange.getOrderBook(marketId, depth);
```

### 特定価格の板の厚み

```typescript
// Side: 0=BUY_YES, 1=SELL_YES, 2=BUY_NO, 3=SELL_NO
const price = ethers.parseUnits("0.65", 6);
const amount = await exchange.getDepthAtPrice(marketId, 0, price);
console.log("$0.65でのBUY_YES板の厚み:", ethers.formatUnits(amount, 6));
```

### 単一注文の照会

```typescript
const order = await exchange.getOrder(orderId);
// 返却値：owner, marketId, side, price, amount（残数量）, timestamp
```

## スプレッド計算

```typescript
const [bestBid, bestAsk] = [bestBidYes, bestAskYes];
const midPrice = (bestBid + bestAsk) / 2n;
const spreadBps = ((bestAsk - bestBid) * 10000n) / midPrice;
console.log("スプレッド (bps):", spreadBps.toString());
```

---

**次へ**: [仮想NO価格設定](virtual-no-pricing.md) · [指値注文](limit-orders.md)
