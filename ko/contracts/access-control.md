---
description: 역할 기반 접근 제어 — 세분화된 권한을 가진 4가지 역할
---

# 접근 제어

## 역할

| 역할 | ID | 목적 |
| ---- | -- | ------- |
| DEFAULT_ADMIN | 0 | 모든 역할 관리 |
| ADMIN | 1 | 마켓 생성, 수수료 설정, Oracle 승인 |
| OPERATOR | 2 | 긴급 결과 확정, 환불 활성화 |
| PAUSER | 3 | 시스템 일시정지 |

## 권한 매트릭스

| 함수 | PUBLIC | ADMIN | OPERATOR | PAUSER | OWNER |
| -------- | ------ | ----- | -------- | ------ | ----- |
| `createMarket` | | ✓ | | | |
| `splitPosition` | ✓ | | | | |
| `mergePositions` | ✓ | | | | |
| `resolveMarket` | ✓ | | | | |
| `redeemMarketTokens` | ✓ | | | | |
| `placeOrder` | ✓ | | | | |
| `buyYes` (Router) | ✓ | | | | |
| `emergencyResolve` | | | ✓ (7일) | | |
| `enableRefundMode` | | | ✓ | | |
| `setTvlCap` | | ✓ | | | |
| `setApprovedOracle` | | ✓ | | | |
| `pauseAll` | | | | ✓ | |
| `diamondCut` | | | | | ✓ |

## 2단계 소유권 이전

```
1. 현재 소유자가 transferOwnership(newOwner) 호출
2. newOwner가 pendingOwner가 됨
3. newOwner가 acceptOwnership() 호출
4. 소유권 이전 완료
```

이를 통해 잘못된 주소로의 실수 이전을 방지합니다.

## 마지막 관리자 보호

시스템은 마지막 관리자의 역할 해제를 방지하여, 프로토콜에 항상 최소 한 명의 관리자가 있도록 보장합니다.

---

**다음**: [안전장치](safety.md) · [Diamond 컨트랙트](diamond.md)
