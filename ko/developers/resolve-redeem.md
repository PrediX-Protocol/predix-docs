---
description: 마켓 정산 및 승리 토큰 상환
---

# 정산 & 상환

## 마켓 정산

오라클이 결과를 보고한 후 누구나 확정할 수 있습니다:

```typescript
// 오라클이 보고했는지 확인
const oracle = new ethers.Contract(ORACLE_ADDRESS, ORACLE_ABI, provider);
const [resolved, outcome] = await oracle.getResolution(marketId);

if (resolved) {
  const tx = await diamond.resolveMarket(marketId);
  await tx.wait();
  console.log("마켓 정산 완료. 결과:", outcome ? "YES" : "NO");
}
```

## 승리 토큰 상환

```typescript
// 승리자는 승리 토큰당 1 USDC를 받습니다
const tx = await diamond.redeemMarketTokens(marketId);
await tx.wait();
```

## 긴급 정산 (OPERATOR 전용)

```typescript
// 오라클이 보고하지 않은 경우 endTime 이후 7일 후 사용 가능
await diamond.emergencyResolve(marketId, true); // true = YES 승리
```

## 환불 모드 (OPERATOR 전용)

```typescript
// 문제가 있는 마켓에 대해 환불 활성화
await diamond.enableRefundMode(marketId);

// 사용자가 비례 환불 청구
await diamond.refund(marketId);
```

---

**다음**: [이벤트](events.md) · [오류](errors.md)
