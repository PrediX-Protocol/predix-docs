---
description: 플러거블 오라클 시스템 - Manual, Chainlink, 커스텀 어댑터
---

# Oracle

PrediX는 플러거블 오라클 아키텍처를 사용합니다. 모든 오라클은 동일 인터페이스를 구현:

```solidity
interface IOracle {
    function getResolution(bytes32 marketId)
        external view returns (bool resolved, bool outcome);
}
```

## ManualOracleAdapter

정치, 스포츠 등 주관적 이벤트용 — 관리자가 결과를 확정.

```solidity
resolve(bytes32 marketId, bool outcome)     // 관리자 전용
getResolution(bytes32 marketId) → (bool resolved, bool outcome)
getResolvedAt(bytes32 marketId) → uint64
```

## ChainlinkAdapter

Chainlink 가격 피드 기반 자동 결과 확정. 예: "BTC > $100K?"

**안전 검증**: stale feed 감지(24시간), L2 시퀀서 업타임, round 완결성.

## 커스텀 오라클 만들기

1. `IOracle` 인터페이스 구현
2. 어댑터 컨트랙트 배포
3. 관리자가 승인: `diamond.setApprovedOracle(adapterAddress, true)`
4. Market 생성 시 사용: `createMarket(..., adapterAddress)`

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
        // 결과 확정 로직
        resolved[marketId] = true;
        outcomes[marketId] = outcome;
    }
}
```

> ⚠️ **Oracle 승인**: 관리자가 승인한 오라클 주소만 market 생성에 사용 가능.

***
