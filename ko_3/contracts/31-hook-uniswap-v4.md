---
description: Uniswap v4 Hook — dynamic fee, 샌드위치 방어, 풀 관리
---

# Hook (Uniswap v4)

PrediXHookV1은 AMM 풀에 prediction market 특화 로직을 추가하는 Uniswap v4 Hook입니다.

### Hook 권한 <a href="#quyen-hook" id="quyen-hook"></a>

| Hook 지점            | 활성화 | 목적                         |
| ------------------ | --- | -------------------------- |
| `beforeInitialize` | ✅   | 풀 등록 검증                    |
| `afterInitialize`  | ✅   | 풀 메타데이터 설정                 |
| `beforeSwap`       | ✅   | Dynamic fee + 샌드위치 방어 체크   |
| `afterSwap`        | ✅   | swap 데이터 기록, 이벤트 emit      |

### Dynamic Fee 계산 <a href="#tinh-phi-dong" id="tinh-phi-dong"></a>

정보 우위 트레이더로부터 LP를 보호하기 위해 market 만료에 가까워질수록 수수료 상승:

| 남은 시간    | 수수료  |
| -------- | ---- |
| > 7일     | 0.5% |
| 3–7일     | 1.0% |
| 1–3일     | 2.0% |
| < 24시간   | 5.0% |

### Anti-Sandwich 방어 <a href="#chong-tan-cong-sandwich" id="chong-tan-cong-sandwich"></a>

Hook은 사용자·풀별로 각 swap을 `(block.number, direction)`으로 추적. 동일 사용자가 같은 block에서 반대 방향 swap을 하면 `SandwichDetected()` 오류로 revert됩니다. **Trusted Router**: Hook은 `msg.sender` 대신 `hookData`에서 실제 사용자 주소를 추출하므로, Router가 거짓 경보 없이 사용자를 대신해 실행 가능.

## 함수

```solidity
// Diamond만 호출 가능
registerMarketPool(bytes32 marketId, address yesToken, PoolKey key)
setTrustedRouter(address router, bool trusted)

// 배포 시 1회 호출
initialize(address diamond, address usdc, address admin)

// 조회
getMarketForPool(PoolId poolId) → bytes32 marketId
```
