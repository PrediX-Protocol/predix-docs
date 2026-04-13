---
description: Diamond 프록시 (EIP-2535) — 마켓 관리, 분할, 병합, 결과 확정, 상환
---

# Diamond 컨트랙트

Diamond는 PrediX Protocol의 중앙 컨트랙트로, 모듈식 업그레이드를 위해 EIP-2535를 구현합니다.

## Facet 목록

| Facet | 함수 |
| ----- | --------- |
| **DiamondCutFacet** | `diamondCut` — Facet 추가/교체/제거 |
| **DiamondLoupeFacet** | `facets`, `facetAddresses`, `facetFunctionSelectors` — 인트로스펙션 |
| **AccessControlFacet** | `grantRole`, `revokeRole`, `hasRole` |
| **OwnableFacet** | `transferOwnership`, `acceptOwnership`, `owner` |
| **PausableFacet** | `pause`, `unpause`, `paused` |
| **MarketFacet** | 모든 마켓 운영 (아래 참조) |

## 작동 방식

Diamond에 대한 모든 함수 호출은 셀렉터로 라우팅됩니다:

```
사용자가 diamond.splitPosition(marketId, amount) 호출
  → Diamond fallback: 셀렉터 0x... 조회 → MarketFacet 주소
  → MarketFacet에 delegatecall
  → MarketFacet이 Diamond의 스토리지를 사용하여 실행
```

## MarketFacet 함수

### 생성 (ADMIN_ROLE)

```solidity
createMarket(string question, string description, uint256 endTime,
             bytes32 categoryId, address oracle) → bytes32 marketId

createCategory(string name, string description, uint256 endTime) → bytes32 categoryId

addMarketToCategory(bytes32 categoryId, bytes32 marketId)
removeMarketFromCategory(bytes32 categoryId, bytes32 marketId)
```

### 포지션 관리 (PUBLIC)

```solidity
splitPosition(bytes32 marketId, uint256 amount)
mergePositions(bytes32 marketId, uint256 amount)
groupSplit(bytes32 categoryId, uint256 amount)
groupMerge(bytes32 categoryId, uint256 amount)
```

### 결과 확정

```solidity
resolveMarket(bytes32 marketId)                              // PUBLIC (오라클 이후)
resolveCategory(bytes32 categoryId, uint256 winningIndex)    // ADMIN_ROLE
emergencyResolve(bytes32 marketId, bool outcome)             // OPERATOR_ROLE (7일 지연)
enableRefundMode(bytes32 marketId)                           // OPERATOR_ROLE
refund(bytes32 marketId)                                     // PUBLIC (환불 모드)
```

### 상환 (PUBLIC)

```solidity
redeemMarketTokens(bytes32 marketId)
redeemCategoryTokens(bytes32 categoryId)
```

### 조회 함수

```solidity
getMarket(bytes32 marketId) → MarketData
getCategory(bytes32 categoryId) → CategoryData
isMarketExists(bytes32 marketId) → bool
isRefundMode(bytes32 marketId) → bool
getSafetyCaps() → (uint256 tvlCap, uint256 perTradeCap, uint256 perMarketCap)
```

### 관리자 (ADMIN_ROLE)

```solidity
setApprovedOracle(address oracle, bool approved)
setTvlCap(uint256 cap)
setPerTradeCap(uint256 cap)
setPerMarketCap(uint256 cap)
```

## MarketData 구조체

```solidity
struct MarketData {
    string question;
    string description;
    uint256 endTime;
    address yesToken;
    address noToken;
    PoolId poolId;
    bytes32 categoryId;
    address oracle;
    bool isResolved;
    bool outcome;           // true = YES 승리, false = NO 승리
    uint256 totalCollateral;
    bytes32 questionHash;   // 사용 중단됨
    uint256 resolvedAt;
}
```

> ⚠️ **중요 셀렉터** (`diamondCut`, `owner`, `transferOwnership`)는 보호되어 있으며 제거할 수 없습니다.

---

**다음**: [Exchange](exchange.md) · [Router](router.md) · [접근 제어](access-control.md)
