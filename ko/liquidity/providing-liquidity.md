---
description: AMM 유동성 제공 단계별 가이드
---

# 유동성 제공

## 개요

AMM 유동성은 PositionManager를 통해 Uniswap v4 YES/USDC 풀에 제공됩니다.

## 단계

### 1. USDC를 YES + NO로 분할

```typescript
const amount = ethers.parseUnits("1000", 6); // 1000 USDC
await usdc.approve(DIAMOND_ADDRESS, amount);
await diamond.splitPosition(marketId, amount);
// 이제 1000 YES + 1000 NO를 보유하게 됩니다
```

### 2. YES와 USDC를 PositionManager에 승인

```typescript
const yesToken = new ethers.Contract(market.yesToken, ERC20_ABI, signer);
await yesToken.approve(POSITION_MANAGER, ethers.MaxUint256);
await usdc.approve(POSITION_MANAGER, ethers.MaxUint256);
```

### 3. LP 포지션 민팅

Uniswap v4 PositionManager를 사용하여 YES/USDC 풀에 유동성 포지션을 생성합니다. 다음 중 선택할 수 있습니다:

- **전체 범위**: $0.01–$0.99를 커버하며, 자본 효율이 낮음
- **집중 범위**: 좁은 범위를 커버 (예: $0.40–$0.70), 자본 효율이 높음

### 4. 제거 시점

- 시장 만료 **최소 24시간 전**에 LP를 제거하세요 (수수료가 5%로 급등할 때)
- 시장을 모니터링하세요 — 결과가 조기에 확실해지면 즉시 제거하세요
- 결과 확정 후 패배 토큰의 가치는 $0입니다

> ⚠️ **참고**: 분할로 얻은 NO 토큰은 별도로 매도하거나 보유할 수 있습니다. LP에는 필요하지 않습니다.

---

**다음**: [마켓 메이킹](market-making.md) · [유동성 개요](overview.md)
