---
description: CLOB 마켓 메이킹 — 오더북 유동성 제공
---

# 마켓 메이킹

## 개요

CLOB에서의 마켓 메이킹은 스프레드를 포착하기 위해 매수 및 매도 주문을 동시에 배치하는 것입니다.

## 기본 전략

```typescript
const exchange = new ethers.Contract(EXCHANGE_ADDRESS, EXCHANGE_ABI, signer);

// 매수(BUY_YES)와 매도(SELL_YES) 주문 배치
const bidPrice = ethers.parseUnits("0.48", 6);
const askPrice = ethers.parseUnits("0.52", 6);
const amount = ethers.parseUnits("100", 6);

// BUY_YES: USDC 예치
await exchange.placeOrder(marketId, 0, bidPrice, amount); // side 0 = BUY_YES

// SELL_YES: YES 토큰 예치 (분할로 보유해야 함)
await exchange.placeOrder(marketId, 1, askPrice, amount); // side 1 = SELL_YES
```

## 스프레드 관리

| 시장 단계 | 권장 스프레드 |
| ------------ | ------------------ |
| 신규 시장 (저거래량) | $0.05–$0.10 |
| 활성 시장 | $0.02–$0.05 |
| 만료 임박 (< 24시간) | $0.05–$0.10 또는 더 넓게 |

## 재고 리스크

한쪽이 다른 쪽보다 더 많이 체결되면 방향성 노출이 누적됩니다:

- YES 토큰이 너무 많음 → YES가 이기면 수익
- NO 토큰이 너무 많음 → NO가 이기면 수익

가격을 조정하거나 split/merge를 사용하여 리밸런싱하세요.

## 마켓 메이커 봇

PrediX는 참조용 마켓 메이커 봇 구현을 제공합니다. 자세한 내용은 GitHub 저장소를 참조하세요.

---

**다음**: [유동성 개요](overview.md) · [지정가 주문](../trading/limit-orders.md)
