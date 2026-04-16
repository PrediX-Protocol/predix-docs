---
description: >-
  Diamond là hợp đồng trung tâm của PrediX Protocol, triển khai EIP-2535 để hỗ
  trợ nâng cấp theo module.
---

# Diamond Proxy (EIP-2535)

## Tại sao sử dụng Diamond Proxy (EIP-2535)

* **Vượt qua giới hạn 24KB**: Logic giao thức phức tạp vượt quá giới hạn kích thước hợp đồng EVM
* **Nâng cấp chi tiết**: Thay thế các hàm riêng lẻ mà không cần triển khai lại toàn bộ
* **Lưu trữ chia sẻ**: Tất cả Facet chia sẻ cùng một bố cục lưu trữ
* **Đã được kiểm chứng trong sản xuất**: Được sử dụng bởi Aavegotchi, Beanstalk và các dự án khác

### Danh sách Facet <a href="#danh-sach-facet" id="danh-sach-facet"></a>

<table data-header-hidden><thead><tr><th width="237"></th><th></th></tr></thead><tbody><tr><td>Facet</td><td>Hàm</td></tr><tr><td><strong>DiamondCutFacet</strong></td><td><code>diamondCut</code> — thêm/thay thế/xóa Facet</td></tr><tr><td><strong>DiamondLoupeFacet</strong></td><td><code>facets</code>, <code>facetAddresses</code>, <code>facetFunctionSelectors</code> — kiểm tra nội bộ</td></tr><tr><td><strong>AccessControlFacet</strong></td><td><code>grantRole</code>, <code>revokeRole</code>, <code>hasRole</code></td></tr><tr><td><strong>OwnableFacet</strong></td><td><code>transferOwnership</code>, <code>acceptOwnership</code>, <code>owner</code></td></tr><tr><td><strong>PausableFacet</strong></td><td><code>pause</code>, <code>unpause</code>, <code>paused</code></td></tr><tr><td><strong>MarketFacet</strong></td><td>Tất cả hoạt động thị trường (xem bên dưới)</td></tr></tbody></table>

## Cách hoạt động

Mọi lệnh gọi hàm đến Diamond được định tuyến bằng selector:

```
Người dùng gọi diamond.splitPosition(marketId, amount)
  → Diamond fallback: tra cứu selector 0x... → địa chỉ MarketFacet
  → delegatecall đến MarketFacet
  → MarketFacet thực thi sử dụng storage của Diamond
```

### Các hàm MarketFacet

## Tạo mới (ADMIN\_ROLE)

<pre class="language-solidity"><code class="lang-solidity"><strong>createMarket(string question, string description, uint256 endTime,
</strong>             bytes32 categoryId, address oracle) → bytes32 marketId

createCategory(string name, string description, uint256 endTime) → bytes32 categoryId

addMarketToCategory(bytes32 categoryId, bytes32 marketId)
removeMarketFromCategory(bytes32 categoryId, bytes32 marketId)
</code></pre>

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

### Quản trị (ADMIN\_ROLE)

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
