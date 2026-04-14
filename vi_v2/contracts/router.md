# Router Contract

Router là giao diện giao dịch chính, gộp thanh khoản từ cả CLOB và AMM để khớp tối ưu.

## Hàm giao dịch (PUBLIC)

```solidity
buyYes(bytes32 marketId, uint256 usdcIn, uint256 minYesOut, address recipient, uint256 deadline) → uint256 yesOut
sellYes(bytes32 marketId, uint256 yesIn, uint256 minUsdcOut, address recipient, uint256 deadline) → uint256 usdcOut
buyNo(bytes32 marketId, uint256 usdcIn, uint256 mintAmount, uint256 minNoOut, address recipient, uint256 deadline) → uint256 noOut
sellNo(bytes32 marketId, uint256 noIn, uint256 minUsdcOut, address recipient, uint256 deadline) → uint256 usdcOut
```

## Hàm quote (VIEW)

```solidity
quoteBuyYes(bytes32 marketId, uint256 usdcIn) → uint256
quoteSellYes(bytes32 marketId, uint256 yesIn) → uint256
quoteBuyNo(bytes32 marketId, uint256 usdcIn) → uint256
quoteSellNo(bytes32 marketId, uint256 noIn) → uint256
```

## Logic routing

```
1. Nhận USDC/token từ user
2. Check depth CLOB ở best price
3. Khớp từ CLOB đến hết depth có sẵn
4. Chuyển phần còn lại sang AMM qua Hook
5. Verify output ≥ minOut (revert nếu không)
6. Chuyển token output cho recipient
```

## Flash Accounting (Uniswap v4)

Router dùng pattern `unlock` callback của Uniswap v4 cho các thao tác nhiều bước atomic:

- **buyNo:** Unlock Pool Manager → split USDC → swap YES lấy USDC trên AMM → trả NO cho user
- **sellNo:** Unlock Pool Manager → swap USDC lấy YES trên AMM → merge YES + NO → trả USDC

Mọi bước chạy atomic. Nếu bất kỳ bước nào fail, cả tx revert.

## Định giá NO ảo

Token NO không có pool riêng. Router tổng hợp NO trade:

- **Buy NO = Split + Sell YES:** `USDC → split → (YES + NO) → bán YES → giữ NO`
- **Sell NO = Buy YES + Merge:** `NO + (mua YES) → merge → USDC`

User chỉ thấy một call đơn giản `buyNo(marketId, usdcIn, ...)`. Router lo phần phức tạp bên trong.

## Tiếp theo

- [Hook](hook.md) — chi tiết AMM
- [Smart Routing](../trading/smart-routing.md) — ví dụ routing
- [Lệnh thị trường](../trading/market-orders.md) — ví dụ code
