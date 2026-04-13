---
description: 전용 AMM 풀 없이 NO 토큰 가격을 책정하는 방법
---

# 가상 NO 가격 책정

NO 토큰은 자체 AMM 풀이 없습니다. Router가 YES/USDC 풀과 분할/병합 작업을 사용하여 NO 거래를 합성합니다.

## NO 매수

```
1. USDC 분할 → YES + NO
2. YES를 AMM 풀에서 매도
3. NO 토큰 보유

사용자는 USDC 지불 → NO 토큰 수령
```

## NO 매도

```
1. AMM 풀에서 YES 매수
2. YES + NO 병합 → USDC
3. USDC를 사용자에게 반환

사용자는 NO 토큰 지불 → USDC 수령
```

## 가격 관계

분할/병합 앵커로 인해:

```
NO 가격 ≈ $1.00 - YES 가격

YES가 $0.70에 거래되는 경우:
  NO ≈ $0.30
```

이는 차익거래에 의해 강제됩니다 - 어떤 편차든 가격을 되돌리는 수익 기회를 만듭니다.

## 사용자 관점에서

Router가 내부적으로 모든 복잡성을 처리합니다. 사용자는 단순히 호출하면 됩니다:

```typescript
// NO 매수 — YES 매수와 동일하게
await router.buyNo(marketId, usdcIn, mintAmount, minNoOut, recipient, deadline);

// NO 매도 — YES 매도와 동일하게
await router.sellNo(marketId, noIn, minUsdcOut, recipient, deadline);
```

> ⚠️ **가스 비용**: NO 거래는 추가 단계(분할 + 스왑 또는 스왑 + 병합)를 포함하므로 가스가 YES 거래보다 약 2.5배 높습니다. CLOB은 이러한 오버헤드 없이 직접 NO 거래를 지원합니다.

---

**다음**: [시장가 주문](market-orders.md) · [스마트 라우팅](smart-routing.md) · [수수료](../concepts/fees.md)
