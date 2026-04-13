---
description: CLOB on-chain với bitmap 99 tick và khớp lệnh ba giai đoạn
---

# Hợp đồng Exchange

PrediXExchange triển khai Sổ lệnh giới hạn trung tâm (CLOB) on-chain với bitmap 99 tick để quét giá hiệu quả về gas.

## Các hàm cốt lõi

### Đặt lệnh

```solidity
placeOrder(bytes32 marketId, uint8 side, uint256 price, uint256 amount)
    → (bytes32 orderId, uint256 filledAmount)
```

Cố gắng khớp với các lệnh hiện có. Phần chưa khớp trở thành lệnh chờ.

### Hủy lệnh

```solidity
cancelOrder(bytes32 orderId)
```

Chủ sở hữu có thể hủy bất cứ lúc nào. Bất kỳ ai cũng có thể hủy sau khi thị trường hết hạn.

### Khớp lệnh thị trường

```solidity
fillMarketOrder(bytes32 marketId, uint8 side, uint256 maxPrice,
                uint256 amount, address taker, address recipient)
    → (uint256 filledAmount, uint256 totalCost)
```

Được Router sử dụng cho phần CLOB trong thực thi kết hợp.

## Hàm truy vấn

```solidity
getOrder(bytes32 orderId) → Order
getBestPrices(bytes32 marketId) → (bestBidYes, bestAskYes, bestBidNo, bestAskNo)
getDepthAtPrice(bytes32 marketId, uint8 side, uint256 price) → uint256
getOrderBook(bytes32 marketId, uint8 depth) → (yesBids, yesAsks, noBids, noAsks)
```

## Thuật toán khớp lệnh

Các lệnh được khớp qua ba giai đoạn:

1. **COMPLEMENTARY**: Đối tác trực tiếp (BUY_YES ↔ SELL_YES)
2. **MINT**: Hai lệnh mua (BUY_YES + BUY_NO, tổng ≥ $1.00)
3. **MERGE**: Hai lệnh bán (SELL_YES + SELL_NO, tổng ≤ $1.00)

## Ràng buộc

| Tham số | Giá trị |
| --------- | ----- |
| Phạm vi giá | `10000` – `990000` ($0.01 – $0.99, 6 chữ số thập phân) |
| Bước giá | `10000` ($0.01) |
| Lệnh tối thiểu | `1000000` ($1.00) |
| Số lệnh tối đa/người dùng/thị trường | 50 |
| Số khớp tối đa mỗi lần đặt lệnh | 20 |

## Cơ chế tạm dừng

```solidity
pause()            // Chỉ PAUSE_ADMIN
unpause()          // Chỉ PAUSE_ADMIN
setPauseAdmin(address) // Chỉ PAUSE_ADMIN hiện tại
```

---

**Tiếp theo**: [Router](router.md) · [Công cụ khớp lệnh](../trading/matching-engine.md) · [Lệnh giới hạn](../trading/limit-orders.md)
