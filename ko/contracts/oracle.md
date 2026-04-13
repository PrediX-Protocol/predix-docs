---
description: 플러거블 오라클 시스템 — Manual, Chainlink 및 커스텀 어댑터
---

# Oracle 시스템

PrediX는 플러거블 오라클 아키텍처를 사용합니다. 모든 오라클은 동일한 인터페이스를 구현합니다:

```solidity
interface IOracle {
    function getResolution(bytes32 marketId)
        external view returns (bool resolved, bool outcome);
}
```

## ManualOracleAdapter

주관적 이벤트(정치, 스포츠 등)를 위한 관리자 제어 결과 확정.

```solidity
resolve(bytes32 marketId, bool outcome)     // 관리자 전용
getResolution(bytes32 marketId) → (bool resolved, bool outcome)
getResolvedAt(bytes32 marketId) → uint64
```

## ChainlinkAdapter

Chainlink 가격 피드 기반 자동 결과 확정. 예: "BTC > $100K?"

**안전 검사**: 오래된 피드 감지(24시간), L2 시퀀서 가동 시간, 라운드 완전성.

## 커스텀 Oracle 생성

1. `IOracle` 인터페이스 구현
2. 어댑터 컨트랙트 배포
3. 관리자 승인: `diamond.setApprovedOracle(adapterAddress, true)`
4. 마켓 생성 시 사용: `createMarket(..., adapterAddress)`

```solidity
contract MyCustomOracle is IOracle {
    mapping(bytes32 => bool) public resolved;
    mapping(bytes32 => bool) public outcomes;

    function getResolution(bytes32 marketId)
        external view returns (bool, bool)
    {
        return (resolved[marketId], outcomes[marketId]);
    }

    function resolve(bytes32 marketId, bool outcome) external {
        // 여기에 결과 확정 로직 작성
        resolved[marketId] = true;
        outcomes[marketId] = outcome;
    }
}
```

> ⚠️ **Oracle 승인**: 마켓 생성 시 관리자가 승인한 Oracle 주소만 사용할 수 있습니다.

---

**다음**: [접근 제어](access-control.md) · [안전장치](safety.md) · [결과 확정 개념](../concepts/resolution.md)
