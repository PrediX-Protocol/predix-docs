# 28. Diamond Proxy (EIP-2535)

### 6 Facets

* **CutFacet:** Thêm/xoá/thay thế functions (upgrade)
* **LoupeFacet:** Introspection — xem facets, functions, interfaces
* **AccessControlFacet:** Quản lý roles (ADMIN, OPERATOR, PAUSER)
* **OwnableFacet:** Two-step ownership transfer, last-admin protection
* **PausableFacet:** Pause từng facet hoặc toàn bộ
* **MarketFacet:** Logic chính — tạo market, split/merge, resolve, redeem

### MarketFacet Functions

**Creation (ADMIN\_ROLE):**

```
createMarket(question, description, endTime, categoryId, oracle) → marketId
createCategory(name, description, endTime) → categoryId
addMarketToCategory(categoryId, marketId)
removeMarketFromCategory(categoryId, marketId)
Position (PUBLIC):
splitPosition(marketId, amount)
mergePositions(marketId, amount)
groupSplit(categoryId, amount)
groupMerge(categoryId, amount)
Resolution:
resolveMarket(marketId) — PUBLIC, after endTime + oracle resolved
resolveCategory(categoryId, winningOutcomeIndex) — ADMIN_ROLE
emergencyResolve(marketId, outcome) — OPERATOR_ROLE, 7d after endTime
enableRefundMode(marketId) — OPERATOR_ROLE
refund(marketId) — PUBLIC, when refundMode active
Redemption (PUBLIC):
redeemMarketTokens(marketId)
redeemCategoryTokens(categoryId)
Views (PUBLIC):
getMarket(marketId) → MarketData
getCategory(categoryId) → CategoryData
isMarketExists(marketId) → bool
isRefundMode(marketId) → bool
getSafetyCaps() → (tvlCap, perTradeCap, perMarketCap)
Admin (ADMIN_ROLE):
setApprovedOracle(oracle, approved)
setTvlCap(cap)
setPerTradeCap(cap)
setPerMarketCap(cap)
```

### MarketData Struct

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
    bool outcome;           // true=YES wins, false=NO wins
    uint256 totalCollateral;
    bytes32 questionHash;   // DEPRECATED
    uint256 resolvedAt;
}
```
