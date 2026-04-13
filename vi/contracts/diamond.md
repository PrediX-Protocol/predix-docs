---
description: Diamond proxy (EIP-2535) — quản lý thị trường, tách, gộp, xác nhận kết quả, đổi thưởng
---

# Hợp đồng Diamond

Diamond là hợp đồng trung tâm của PrediX Protocol, triển khai EIP-2535 để hỗ trợ nâng cấp theo module.

## Danh sách Facet

| Facet | Hàm |
| ----- | --------- |
| **DiamondCutFacet** | `diamondCut` — thêm/thay thế/xóa Facet |
| **DiamondLoupeFacet** | `facets`, `facetAddresses`, `facetFunctionSelectors` — kiểm tra nội bộ |
| **AccessControlFacet** | `grantRole`, `revokeRole`, `hasRole` |
| **OwnableFacet** | `transferOwnership`, `acceptOwnership`, `owner` |
| **PausableFacet** | `pause`, `unpause`, `paused` |
| **MarketFacet** | Tất cả hoạt động thị trường (xem bên dưới) |

## Cách hoạt động

Mọi lệnh gọi hàm đến Diamond được định tuyến bằng selector:

```
Người dùng gọi diamond.splitPosition(marketId, amount)
  → Diamond fallback: tra cứu selector 0x... → địa chỉ MarketFacet
  → delegatecall đến MarketFacet
  → MarketFacet thực thi sử dụng storage của Diamond
```

## Các hàm MarketFacet

### Tạo mới (ADMIN_ROLE)

```solidity
createMarket(string question, string description, uint256 endTime,
             bytes32 categoryId, address oracle) → bytes32 marketId

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

### Xác nhận kết quả

```solidity
resolveMarket(bytes32 marketId)                              // PUBLIC (sau oracle)
resolveCategory(bytes32 categoryId, uint256 winningIndex)    // ADMIN_ROLE
emergencyResolve(bytes32 marketId, bool outcome)             // OPERATOR_ROLE (trì hoãn 7 ngày)
enableRefundMode(bytes32 marketId)                           // OPERATOR_ROLE
refund(bytes32 marketId)                                     // PUBLIC (chế độ hoàn tiền)
```

### Đổi thưởng (PUBLIC)

```solidity
redeemMarketTokens(bytes32 marketId)
redeemCategoryTokens(bytes32 categoryId)
```

### Hàm truy vấn

```solidity
getMarket(bytes32 marketId) → MarketData
getCategory(bytes32 categoryId) → CategoryData
isMarketExists(bytes32 marketId) → bool
isRefundMode(bytes32 marketId) → bool
getSafetyCaps() → (uint256 tvlCap, uint256 perTradeCap, uint256 perMarketCap)
```

### Quản trị (ADMIN_ROLE)

```solidity
setApprovedOracle(address oracle, bool approved)
setTvlCap(uint256 cap)
setPerTradeCap(uint256 cap)
setPerMarketCap(uint256 cap)
```

## Cấu trúc MarketData

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
    bytes32 questionHash;   // KHÔNG CÒN SỬ DỤNG
    uint256 resolvedAt;
}
```

> ⚠️ **Các selector quan trọng** (`diamondCut`, `owner`, `transferOwnership`) được bảo vệ và không thể bị xóa.

---

**Tiếp theo**: [Exchange](exchange.md) · [Router](router.md) · [Kiểm soát truy cập](access-control.md)
