---
description: Truy vấn sổ lệnh trên chuỗi — giá, độ sâu và chênh lệch giá
---

# Sổ lệnh

## Các hàm xem

### Giá tốt nhất

```typescript
const [bestBidYes, bestAskYes, bestBidNo, bestAskNo] =
  await exchange.getBestPrices(marketId);

console.log("Giá mua YES:", ethers.formatUnits(bestBidYes, 6));
console.log("Giá bán YES:", ethers.formatUnits(bestAskYes, 6));
console.log("Chênh lệch:", ethers.formatUnits(bestAskYes - bestBidYes, 6));
```

### Toàn bộ sổ lệnh

```typescript
// depth = số mức giá cần trả về
const depth = 10;
const [yesBids, yesAsks, noBids, noAsks] =
  await exchange.getOrderBook(marketId, depth);
```

### Độ sâu tại giá cụ thể

```typescript
// Side: 0=BUY_YES, 1=SELL_YES, 2=BUY_NO, 3=SELL_NO
const price = ethers.parseUnits("0.65", 6);
const amount = await exchange.getDepthAtPrice(marketId, 0, price);
console.log("Độ sâu BUY_YES tại $0.65:", ethers.formatUnits(amount, 6));
```

### Truy vấn lệnh đơn

```typescript
const order = await exchange.getOrder(orderId);
// Trả về: owner, marketId, side, price, amount (còn lại), timestamp
```

## Tính toán chênh lệch giá

```typescript
const [bestBid, bestAsk] = [bestBidYes, bestAskYes];
const midPrice = (bestBid + bestAsk) / 2n;
const spreadBps = ((bestAsk - bestBid) * 10000n) / midPrice;
console.log("Chênh lệch (bps):", spreadBps.toString());
```

---

**Tiếp theo**: [Định giá NO ảo](virtual-no-pricing.md) · [Lệnh giới hạn](limit-orders.md)
