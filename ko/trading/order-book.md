---
description: 온체인 오더북 조회 - 가격, 깊이 및 스프레드
---

# 오더북

## 조회 함수

### 최우선 가격

```typescript
const [bestBidYes, bestAskYes, bestBidNo, bestAskNo] =
  await exchange.getBestPrices(marketId);

console.log("YES 매수호가:", ethers.formatUnits(bestBidYes, 6));
console.log("YES 매도호가:", ethers.formatUnits(bestAskYes, 6));
console.log("스프레드:", ethers.formatUnits(bestAskYes - bestBidYes, 6));
```

### 전체 오더북

```typescript
// depth = 반환할 가격 레벨 수
const depth = 10;
const [yesBids, yesAsks, noBids, noAsks] =
  await exchange.getOrderBook(marketId, depth);
```

### 특정 가격의 깊이

```typescript
// Side: 0=BUY_YES, 1=SELL_YES, 2=BUY_NO, 3=SELL_NO
const price = ethers.parseUnits("0.65", 6);
const amount = await exchange.getDepthAtPrice(marketId, 0, price);
console.log("$0.65에서 BUY_YES 깊이:", ethers.formatUnits(amount, 6));
```

### 단일 주문 조회

```typescript
const order = await exchange.getOrder(orderId);
// 반환: owner, marketId, side, price, amount (잔여), timestamp
```

## 스프레드 계산

```typescript
const [bestBid, bestAsk] = [bestBidYes, bestAskYes];
const midPrice = (bestBid + bestAsk) / 2n;
const spreadBps = ((bestAsk - bestBid) * 10000n) / midPrice;
console.log("스프레드 (bps):", spreadBps.toString());
```

---

**다음**: [가상 NO 가격 책정](virtual-no-pricing.md) · [지정가 주문](limit-orders.md)
