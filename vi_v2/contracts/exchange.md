# Exchange Contract

Exchange cài đặt sổ lệnh giới hạn (CLOB) hoàn toàn on-chain với bitmap 99-tick để quản lý price level hiệu quả.

## Cấu trúc order book

- **99 price tick:** $0.01 → $0.99, bước $0.01
- **Bitmap index:** Lookup best price O(1)
- **FIFO trong cùng price:** Lệnh đến trước được khớp trước

## Hàm

### placeOrder

```solidity
placeOrder(bytes32 marketId, uint8 side, uint256 price, uint256 amount)
  → (bytes32 orderId, uint256 filledAmount)
```

Đặt limit order. Có thể khớp một phần ngay nếu có lệnh đối ứng. Trả về `orderId` và phần `filledAmount`.

### cancelOrder

```solidity
cancelOrder(bytes32 orderId)
```

Huỷ lệnh của chính bạn. Sau khi market hết hạn, bất cứ ai cũng huỷ được bất kỳ lệnh nào.

### fillMarketOrder

```solidity
fillMarketOrder(bytes32 marketId, uint8 side, uint256 maxPrice, uint256 amount, address taker, address recipient)
  → (uint256 filledAmount, uint256 totalCost)
```

Thực thi market order khớp với các lệnh đang nằm chờ. Router dùng hàm này nội bộ.

### View

```solidity
getOrder(bytes32 orderId) → Order
getBestPrices(bytes32 marketId) → (bestBidYes, bestAskYes, bestBidNo, bestAskNo)
getDepthAtPrice(bytes32 marketId, uint8 side, uint256 price) → uint256
getOrderBook(bytes32 marketId, uint8 depth) → (yesBids, yesAsks, noBids, noAsks)
```

### Pause

```solidity
pause()       // PAUSE_ADMIN
unpause()     // PAUSE_ADMIN
setPauseAdmin(address)  // PAUSE_ADMIN
```

## Thuật toán khớp 3 giai đoạn

Khi `placeOrder` được gọi:

1. **Phase 1 — COMPLEMENTARY:** Khớp với lệnh đối ứng (BUY_YES ↔ SELL_YES)
2. **Phase 2 — MINT:** Khớp BUY_YES + BUY_NO khi tổng giá ≥ $1.00
3. **Phase 3 — MERGE:** Khớp SELL_YES + SELL_NO khi tổng giá ≤ $1.00

Chi tiết xem [Matching Engine](../trading/matching-engine.md).

## Tiếp theo

- [Router](router.md) — cách Router dùng Exchange
- [Order Book](../trading/order-book.md) — đọc dữ liệu sổ lệnh
- [Lệnh giới hạn](../trading/limit-orders.md) — ví dụ code
