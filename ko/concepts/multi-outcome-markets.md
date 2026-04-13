---
description: 카테고리 - 두 가지 이상의 결과를 가진 마켓
---

# 다중 결과 마켓

## 카테고리

**카테고리**는 여러 YES/NO 마켓을 하나의 다중 결과 이벤트로 묶습니다.

### 예시: "2028년 미국 대통령 선거에서 누가 승리할까?"

```
Category: "US President 2028"
  Market 0: "Trump wins?"     → YES/NO 토큰
  Market 1: "DeSantis wins?"  → YES/NO 토큰
  Market 2: "Newsom wins?"    → YES/NO 토큰
  Market 3: "Harris wins?"    → YES/NO 토큰
  Market 4: "Other wins?"     → YES/NO 토큰
```

## GroupSplit & GroupMerge

### GroupSplit

`amount × N` USDC를 예치하면 (N = 카테고리 내 마켓 수) 각 마켓당 `amount`개의 YES + NO를 받습니다.

```typescript
// 카테고리에 5개의 마켓. 500 USDC 예치 → 마켓당 100 YES+NO
const amount = ethers.parseUnits("100", 6);
// 100 × 5 = 500 USDC 승인 필요
await usdc.approve(DIAMOND_ADDRESS, ethers.parseUnits("500", 6));
await diamond.groupSplit(categoryId, amount);
```

### GroupMerge

모든 마켓에서 `amount`개의 YES + NO를 소각하여 `amount × N` USDC를 회수합니다.

```typescript
await diamond.groupMerge(categoryId, amount);
// 5개 마켓 각각에서 100 YES + 100 NO 소각
// 500 USDC 반환
```

## 결과 확정

```typescript
// 승자는 1개: 인덱스 2의 마켓이 승리
await diamond.resolveCategory(categoryId, 2);

// 결과:
// Market 2 → YES 승리 (YES 보유자 $1 상환)
// Markets 0, 1, 3, 4 → NO 승리 (NO 보유자 $1 상환)
// 모든 마켓이 원자적으로 결과 확정
```

> ⚠️ **중요**: `resolveCategory`는 ADMIN 역할에 의해 호출되며, 카테고리 내 모든 마켓을 동시에 결과 확정합니다.

---

**다음**: [수수료](fees.md) · [거래 개요](../trading/overview.md) · [마켓 생성 (개발자)](../developers/create-market.md)
