---
description: 마켓 결과 확정 방법과 승자 상환 절차
---

# 결과 확정

## 마켓 생명주기

```
생성됨 → 활성 (거래 중) → 만료됨 → 결과 확정됨 → 상환 가능
                           ↓
                   비상 (7일) → 비상 결과 확정
                           ↓
                   환불 모드 → 사용자 환불
```

## 일반 결과 확정

1. 마켓이 `endTime`에 도달 (거래는 계속되지만 새로운 분할은 불가)
2. 오라클이 결과 보고: `resolve(marketId, true/false)`
3. 누구나 Diamond에서 `resolveMarket(marketId)` 호출 가능
4. 마켓이 결과 확정됨으로 표시
5. 승자가 `redeemMarketTokens(marketId)` 호출

```typescript
// 1단계: 결과 확정 (오라클 보고 후 누구나 호출 가능)
await diamond.resolveMarket(marketId);

// 2단계: 승리 토큰 상환
await diamond.redeemMarketTokens(marketId);
// 승자는 승리 토큰당 1 USDC를 받음
```

## 비상 결과 확정

`endTime` 이후 7일 이내에 오라클이 보고하지 않는 경우:

1. `OPERATOR`가 `emergencyResolve(marketId, outcome)`를 호출 가능
2. 오라클을 우회하여 결과를 직접 설정

> ⚠️ 비상 결과 확정은 `endTime` 이후 **7일의 지연**이 필요하며, OPERATOR 역할만 호출할 수 있습니다.

## 환불 모드

마켓이 무효 또는 문제가 있다고 판단될 경우:

1. `OPERATOR`가 `enableRefundMode(marketId)` 호출
2. 모든 거래가 차단됨 (분할, 병합, CLOB, AMM)
3. 토큰 보유자가 `refund(marketId)` 호출
4. 각 보유자는 YES + NO 잔액에 비례하여 USDC를 받음

```typescript
// 운영자가 환불 활성화
await diamond.enableRefundMode(marketId);

// 사용자가 비례 환불 청구
await diamond.refund(marketId);
```

## 카테고리 결과 확정

다중 결과 마켓의 경우:

```typescript
// 관리자가 카테고리 결과 확정: 승자는 결과 인덱스 2
await diamond.resolveCategory(categoryId, 2);
// 카테고리 내 모든 마켓이 원자적으로 결과 확정
```

---

**다음**: [다중 결과 마켓](multi-outcome-markets.md) · [수수료](fees.md) · [오라클 컨트랙트](../contracts/oracle.md)
