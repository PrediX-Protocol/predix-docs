---
description: >-
  Diamond는 PrediX Protocol의 중심 컨트랙트로, 모듈 업그레이드를 지원하기 위해 EIP-2535를
  구현합니다.
---

# Diamond Proxy (EIP-2535)

## 왜 Diamond Proxy (EIP-2535)인가

* **24KB 제한 극복**: 복잡한 프로토콜 로직은 EVM 컨트랙트 크기 제한을 초과
* **세밀한 업그레이드**: 전체 재배포 없이 개별 함수 교체
* **공유 스토리지**: 모든 Facet이 동일한 스토리지 레이아웃 공유
* **프로덕션 검증**: Aavegotchi, Beanstalk 등에서 사용

### Facet 목록 <a href="#danh-sach-facet" id="danh-sach-facet"></a>

<table data-header-hidden><thead><tr><th width="237"></th><th></th></tr></thead><tbody><tr><td>Facet</td><td>함수</td></tr><tr><td><strong>DiamondCutFacet</strong></td><td><code>diamondCut</code> — Facet 추가/교체/삭제</td></tr><tr><td><strong>DiamondLoupeFacet</strong></td><td><code>facets</code>, <code>facetAddresses</code>, <code>facetFunctionSelectors</code> — 내부 조회</td></tr><tr><td><strong>AccessControlFacet</strong></td><td><code>grantRole</code>, <code>revokeRole</code>, <code>hasRole</code></td></tr><tr><td><strong>OwnableFacet</strong></td><td><code>transferOwnership</code>, <code>acceptOwnership</code>, <code>owner</code></td></tr><tr><td><strong>PausableFacet</strong></td><td><code>pause</code>, <code>unpause</code>, <code>paused</code></td></tr><tr><td><strong>MarketFacet</strong></td><td>모든 market 운영 (아래 참조)</td></tr></tbody></table>

## 작동 방식

Diamond로의 모든 함수 호출은 selector로 라우팅됩니다:

```
사용자가 diamond.splitPosition(marketId, amount) 호출
  → Diamond fallback: selector 0x... → MarketFacet 주소 조회
  → MarketFacet으로 delegatecall
  → MarketFacet이 Diamond의 storage를 사용해 실행
```

### MarketFacet 함수

## 생성 (ADMIN\_ROLE)

<pre class="language-solidity"><code class="lang-solidity"><strong>createMarket(string question, string description, uint256 endTime,
</strong>             bytes32 categoryId, address oracle) → bytes32 marketId

createCategory(string name, string description, uint256 endTime) → bytes32 categoryId

addMarketToCategory(bytes32 categoryId, bytes32 marketId)
removeMarketFromCategory(bytes32 categoryId, bytes32 marketId)
</code></pre>

### Redemption (PUBLIC)

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

### 관리 (ADMIN\_ROLE)

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
    bytes32 questionHash;   // DEPRECATED
    uint256 resolvedAt;
}
```

> ⚠️ **중요 selector** (`diamondCut`, `owner`, `transferOwnership`)는 보호되어 삭제할 수 없습니다.
