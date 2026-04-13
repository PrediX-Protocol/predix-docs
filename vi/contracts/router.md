---
description: Smart Router — thực thi kết hợp CLOB + AMM qua Uniswap v4
---

# Hợp đồng Router

PrediXRouter tổng hợp thanh khoản từ CLOB (Exchange) và AMM (Uniswap v4) để cung cấp thực thi tốt nhất cho nhà giao dịch.

## Các hàm giao dịch

```solidity
buyYes(bytes32 marketId, uint256 usdcIn, uint256 minYesOut,
       address recipient, uint256 deadline) → uint256 yesOut

sellYes(bytes32 marketId, uint256 yesIn, uint256 minUsdcOut,
        address recipient, uint256 deadline) → uint256 usdcOut

buyNo(bytes32 marketId, uint256 usdcIn, uint256 mintAmount,
      uint256 minNoOut, address recipient, uint256 deadline) → uint256 noOut

sellNo(bytes32 marketId, uint256 noIn, uint256 minUsdcOut,
       address recipient, uint256 deadline) → uint256 usdcOut
```

## Các hàm báo giá

```solidity
quoteBuyYes(bytes32 marketId, uint256 usdcIn) → uint256
quoteSellYes(bytes32 marketId, uint256 yesIn) → uint256
quoteBuyNo(bytes32 marketId, uint256 usdcIn) → uint256
quoteSellNo(bytes32 marketId, uint256 noIn) → uint256
```

## Logic định tuyến

```
buyYes($100):
  1. Kiểm tra lệnh SELL_YES trên CLOB có giá tốt hơn AMM
  2. Khớp CLOB (không trượt giá, giá giới hạn)
  3. Định tuyến phần còn lại sang Uniswap v4 AMM swap
  4. Trả tổng YES cho người nhận
```

## Định giá NO ảo

Vì chỉ có pool YES/USDC:

- **Mua NO**: Tách USDC → YES+NO → bán YES qua AMM → giữ NO
- **Bán NO**: Mua YES từ AMM → gộp YES+NO → trả USDC

## Flash Accounting

Router sử dụng Flash Accounting của Uniswap v4 (mẫu unlock callback) để thực hiện các thao tác nhiều bước hiệu quả về gas.

> ⚠️ **Tham số**: Luôn đặt `deadline` (thường là 5 phút) và `minOut` (bảo vệ trượt giá). Giao dịch sẽ bị hoàn lại nếu điều kiện không được đáp ứng.

---

**Tiếp theo**: [Hook](hook.md) · [Định tuyến thông minh](../trading/smart-routing.md) · [Lệnh thị trường](../trading/market-orders.md)
