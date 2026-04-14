# Diamond Contract

Diamond proxy (EIP-2535) là contract lõi quản lý market, vị thế và phân xử. Mọi call được route theo selector sang facet tương ứng.

## Các facet

| Facet | Trách nhiệm |
|-------|---------------|
| **DiamondCutFacet** | Thêm/replace/xoá facet |
| **DiamondLoupeFacet** | Introspection (liệt kê facet, selector) |
| **AccessControlFacet** | Quản lý role (4 role) |
| **OwnableFacet** | Chuyển quyền sở hữu 2 bước |
| **PausableFacet** | Pause khẩn cấp theo facet hoặc toàn cục |
| **MarketFacet** | Market, vị thế, phân xử, redeem |

## Function routing hoạt động ra sao

```
User gọi Diamond.splitPosition(marketId, amount)
  → Fallback Diamond check selector 0x...
  → Route sang MarketFacet.splitPosition()
  → Thực thi trong storage của Diamond
```

## Hàm MarketFacet

### Tạo market (ADMIN_ROLE)

```solidity
createMarket(string question, string description, uint256 endTime, bytes32 categoryId, address oracle) → bytes32 marketId
createCategory(string name, string description, uint256 endTime) → bytes32 categoryId
addMarketToCategory(bytes32 categoryId, bytes32 marketId)
removeMarketFromCategory(bytes32 categoryId, bytes32 marketId)
```

### Quản lý vị thế (PUBLIC)

```solidity
splitPosition(bytes32 marketId, uint256 amount)
mergePositions(bytes32 marketId, uint256 amount)
groupSplit(bytes32 categoryId, uint256 amount)
groupMerge(bytes32 categoryId, uint256 amount)
```

Cơ chế split/merge xem tại [Split & Merge](../concepts/split-and-merge.md).

### Phân xử

```solidity
resolveMarket(bytes32 marketId)                                    // PUBLIC — sau endTime + oracle đã resolve
resolveCategory(bytes32 categoryId, uint256 winningOutcomeIndex)   // ADMIN_ROLE
emergencyResolve(bytes32 marketId, bool outcome)                   // OPERATOR_ROLE — 7d sau endTime
enableRefundMode(bytes32 marketId)                                 // OPERATOR_ROLE
refund(bytes32 marketId)                                           // PUBLIC — khi refundMode bật
```

Flow phân xử xem tại [Phân xử](../concepts/resolution.md).

### Redemption (PUBLIC)

```solidity
redeemMarketTokens(bytes32 marketId)
redeemCategoryTokens(bytes32 categoryId)
```

### View (PUBLIC)

```solidity
getMarket(bytes32 marketId) → MarketData
getCategory(bytes32 categoryId) → CategoryData
isMarketExists(bytes32 marketId) → bool
isRefundMode(bytes32 marketId) → bool
getSafetyCaps() → (tvlCap, perTradeCap, perMarketCap)
```

### Admin (ADMIN_ROLE)

```solidity
setApprovedOracle(address oracle, bool approved)
setTvlCap(uint256 cap)
setPerTradeCap(uint256 cap)
setPerMarketCap(uint256 cap)
```

## Struct MarketData

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
    bool outcome;           // true = YES thắng, false = NO thắng
    uint256 totalCollateral;
    bytes32 questionHash;   // DEPRECATED
    uint256 resolvedAt;
}
```

## Quy trình nâng cấp

Chỉ `DEFAULT_ADMIN_ROLE` được gọi `diamondCut()` để thêm, thay thế, hoặc xoá facet. Mỗi lần nâng cấp chỉ định mảng địa chỉ facet và selector tương ứng.

## Tiếp theo

- [Exchange](exchange.md) — sổ lệnh CLOB
- [Bảo mật](security.md) — các role và safety cap
- [Developer: Tạo Market](../developers/create-market.md) — code step-by-step
