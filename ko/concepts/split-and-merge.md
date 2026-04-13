---
description: USDC를 결과 토큰으로 분할하거나 다시 병합
---

# 분할 & 병합

분할과 병합은 결과 토큰 가격을 실제 가치에 고정하는 기본 메커니즘입니다.

## 분할

USDC 담보를 예치하여 동일한 수량의 YES 및 NO 토큰을 받습니다:

```
1 USDC → 1 YES + 1 NO
```

담보는 Diamond 컨트랙트에 잠깁니다. 양쪽 토큰 공급량이 동일하게 증가합니다.

## 병합

동일한 수량의 YES 및 NO 토큰을 소각하여 USDC를 인출합니다:

```
1 YES + 1 NO → 1 USDC
```

담보는 Diamond에서 해제됩니다. 양쪽 토큰 공급량이 동일하게 감소합니다.

## 이것이 중요한 이유

분할/병합은 전체 마켓의 **가격 앵커**를 생성합니다:

```
YES = $0.70이고 NO = $0.40인 경우:
  YES + NO = $1.10 > $1.00
  → 차익거래자: $1 분할 → YES $0.70 매도 + NO $0.40 매도 = $1.10
  → 이익: $0.10
  → 매도 압력이 가격을 합계 ≈ $1.00으로 되돌림
```

## 코드 예시

```typescript
import { ethers } from "ethers";

const diamond = new ethers.Contract(DIAMOND_ADDRESS, DIAMOND_ABI, signer);
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, signer);

const marketId = "0x...";
const amount = ethers.parseUnits("100", 6); // 100 USDC

// 1단계: USDC를 Diamond에 승인
await usdc.approve(DIAMOND_ADDRESS, amount);

// 2단계: 분할 — 100 YES + 100 NO 수령
await diamond.splitPosition(marketId, amount);

// 3단계: 병합 — 100 YES + 100 NO 소각, 100 USDC 수령
await diamond.mergePositions(marketId, amount);
```

## 안전 한도

분할 작업에는 안전 한도가 적용됩니다:

| 한도 | 설명 |
| ---- | ---- |
| **TVL 한도** | 시스템에 잠길 수 있는 최대 USDC |
| **거래당 한도** | 단일 분할 작업당 최대 USDC |
| **마켓당 한도** | 개별 마켓당 최대 USDC |

> ⚠️ **참고**: 병합하려면 동일한 수량의 YES와 NO 토큰을 보유해야 합니다. 병합 수량은 어느 쪽 토큰 잔액도 초과할 수 없습니다.

---

**다음**: [결과 확정](resolution.md) · [다중 결과 마켓](multi-outcome-markets.md) · [거래 개요](../trading/overview.md)
