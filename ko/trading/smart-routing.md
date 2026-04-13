---
description: Router가 CLOB과 AMM 유동성을 집계하는 방법
---

# 스마트 라우팅

PrediXRouter는 CLOB과 AMM 양쪽에서 자동으로 최적의 실행 경로를 찾습니다.

## 작동 방식

```
router.buyYes($100):
  1단계: CLOB에서 AMM보다 유리한 가격의 SELL_YES 주문 확인
  2단계: CLOB 주문을 먼저 체결 (최적 가격, 슬리피지 없음)
  3단계: 잔여 수량을 Uniswap v4 AMM으로 라우팅
  4단계: 사용자가 양쪽 소스에서 총 YES 토큰 수령
```

### 예시

```
100 YES 토큰 매수:
  CLOB: 60 YES 이용 가능 @ $0.62 → 비용: $37.20
  AMM:  40 YES @ 평균 ~$0.65   → 비용: $26.00
  합계: 100 YES를 $63.20에 구매

Smart Router 없이 (AMM만 사용):
  100 YES @ 평균 ~$0.66 → 비용: $66.00
  
  절감액: $2.80 (4.2% 더 나은 체결)
```

## 시세 조회 함수

거래 전에 항상 시세를 조회하세요:

```typescript
const router = new ethers.Contract(ROUTER_ADDRESS, ROUTER_ABI, provider);

const yesOut = await router.quoteBuyYes(marketId, usdcIn);
const usdcOut = await router.quoteSellYes(marketId, yesIn);
const noOut = await router.quoteBuyNo(marketId, usdcIn);
const usdcFromNo = await router.quoteSellNo(marketId, noIn);
```

## CLOB이 AMM보다 유리한 경우

| 조건 | 더 나은 거래소 |
| ---- | -------------- |
| 깊은 오더북, 좁은 스프레드 | CLOB |
| 원하는 가격에 주문 없음 | AMM |
| 대량 주문 | 양쪽 분산 |
| 마켓 만료 임박 (높은 AMM 수수료) | CLOB |

---

**다음**: [매칭 엔진](matching-engine.md) · [가상 NO 가격 책정](virtual-no-pricing.md)
