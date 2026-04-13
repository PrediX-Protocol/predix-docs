---
description: Uniswap v4 Hook — 동적 수수료, 샌드위치 공격 방지, 풀 관리
---

# Hook 컨트랙트

PrediXHookV1은 AMM 풀에 예측 시장 전용 로직을 추가하는 Uniswap v4 Hook입니다.

## Hook 권한

| Hook 포인트 | 활성화 | 목적 |
| ---------- | ------- | ------- |
| `beforeInitialize` | ✅ | 풀 등록 검증 |
| `afterInitialize` | ✅ | 풀 메타데이터 설정 |
| `beforeSwap` | ✅ | 동적 수수료 + 샌드위치 공격 검사 |
| `afterSwap` | ✅ | 스왑 데이터 기록, 이벤트 발행 |

## 동적 수수료 계산

마켓 만료가 가까워질수록 수수료가 증가하여 정보를 가진 트레이더로부터 LP를 보호합니다:

| 잔여 시간 | 수수료 |
| -------------- | --- |
| > 7일 | 0.5% |
| 3~7일 | 1.0% |
| 1~3일 | 2.0% |
| < 24시간 | 5.0% |

## 샌드위치 공격 방지

Hook은 사용자별 풀별로 각 스왑을 `(block.number, direction)`으로 추적합니다. 동일 사용자가 같은 블록 내에서 반대 방향으로 스왑하면 `SandwichDetected()` 오류와 함께 트랜잭션이 되돌려집니다.

**신뢰된 Router**: Hook은 `msg.sender` 대신 `hookData`에서 실제 사용자 주소를 추출하므로, Router가 사용자를 대신하여 실행할 때 오탐이 발생하지 않습니다.

## 함수

```solidity
// Diamond만 호출 가능
registerMarketPool(bytes32 marketId, address yesToken, PoolKey key)
setTrustedRouter(address router, bool trusted)

// 배포 시 한 번만 호출
initialize(address diamond, address usdc, address admin)

// 조회
getMarketForPool(PoolId poolId) → bytes32 marketId
```

---

**다음**: [Oracle](oracle.md) · [수수료](../concepts/fees.md) · [안전장치](safety.md)
