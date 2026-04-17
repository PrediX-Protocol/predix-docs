---
description: CLOB on-chain với bitmap 99 tick và khớp lệnh ba giai đoạn
---

# Exchange (CLOB)

## On-Chain Order Book

PrediXExchange triển khai Sổ lệnh giới hạn trung tâm (CLOB) on-chain với 99-tick bitmap ($0,01–$0,99) để quét giá hiệu quả về gas. Mỗi tick = 10000 đơn vị (6 decimals). Orders lưu trên-chain.

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

1. **COMPLEMENTARY**: Đối tác trực tiếp (BUY\_YES ↔ SELL\_YES)
2. **MINT**: Hai lệnh mua (BUY\_YES + BUY\_NO, tổng ≥ $1.00)
3. **MERGE**: Hai lệnh bán (SELL\_YES + SELL\_NO, tổng ≤ $1.00)

## Ràng buộc

<table><thead><tr><th width="307">Tham số</th><th>Giá trị</th></tr></thead><tbody><tr><td>Phạm vi giá</td><td><code>10000</code> – <code>990000</code> ($0.01 – $0.99, 6 chữ số thập phân)</td></tr><tr><td>Bước giá</td><td><code>10000</code> ($0.01)</td></tr><tr><td>Lệnh tối thiểu</td><td><code>1000000</code> ($1.00)</td></tr><tr><td>Số lệnh tối đa/người dùng/thị trường</td><td>50</td></tr><tr><td>Số khớp tối đa mỗi lần đặt lệnh</td><td>20</td></tr></tbody></table>

## Cơ chế tạm dừng

```solidity
pause()            // Chỉ PAUSE_ADMIN
unpause()          // Chỉ PAUSE_ADMIN
setPauseAdmin(address) // Chỉ PAUSE_ADMIN hiện tại
```
