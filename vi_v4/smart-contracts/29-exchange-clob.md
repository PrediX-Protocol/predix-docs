---
description: PrediXExchange — CLOB on-chain với bitmap 99 tick, 3 MatchType, solvency-safe.
---

# PrediXExchange (CLOB)

`PrediXExchange` là Central Limit Order Book on-chain. User/Router post limit orders, contract match theo price-time priority với 3 loại khớp lệnh.

Source: `SC/packages/exchange/src/PrediXExchange.sol`
Interface: `SC/packages/exchange/src/IPrediXExchange.sol`

## Thiết kế cốt lõi

- **Price range**: \$0.01–\$0.99 với step \$0.01 → 99 ticks. Internally: `10000`–`990000` (6 decimals, step `10000`).
- **Bitmap 99 ticks**: quét giá hiệu quả về gas, `O(1)` tìm best price per side.
- **Orders lưu on-chain**: packed vào 5 storage slot/order.
- **Solvency invariant** (INV-2): `Σ order.depositLocked == IERC20(usdc).balanceOf(exchange) + Σ outcomeToken.balanceOf(exchange)`.

## Enum Side (quan trọng — developer)

```solidity
enum Side {
    BUY_YES,   // 0
    SELL_YES,  // 1
    BUY_NO,    // 2
    SELL_NO    // 3
}
```

## Enum MatchType (3 loại)

```solidity
enum MatchType {
    COMPLEMENTARY,  // 0 — BUY_YES ↔ SELL_YES hoặc BUY_NO ↔ SELL_NO
    MINT,           // 1 — BUY_YES + BUY_NO (tổng ≥ $1), Diamond mint cặp YES+NO
    MERGE           // 2 — SELL_YES + SELL_NO (tổng ≤ $1), Diamond merge burn
}
```

Chi tiết matching: [Matching Engine](../giao-dich/25-matching-engine.md).

## Core functions

### `placeOrder`

```solidity
function placeOrder(
    uint256 marketId,
    Side side,
    uint256 price,
    uint256 amount
) external returns (bytes32 orderId, uint256 filledAmount);
```

**Role**: Public
**Checks**:
- `price` in `[10000, 990000]` step `10000` → else `Exchange_InvalidPrice`
- `amount >= MIN_ORDER_SIZE (1000000 = $1)` → else `Exchange_OrderTooSmall`
- Market active + not resolved + not in refund mode
- User chưa đạt `MAX_ORDERS_PER_USER_PER_MARKET (50)` → else `Exchange_MaxOrdersExceeded`

**Effects**: Contract cố gắng fill từ orderbook đối diện; unfilled → store as resting order.

**Event**: `OrderPlaced(orderId, marketId, owner, side, price, amount)` → optional nhiều `OrderMatched`.

### `cancelOrder`

```solidity
function cancelOrder(bytes32 orderId) external;
```

**Role**:
- Trong thời gian market còn mở: **chỉ owner** mới cancel được
- Sau khi market kết thúc: **bất kỳ ai** cũng có thể cancel giúp (unlock collateral cho owner)

**Event**: `OrderCancelled(orderId)`

### `fillAsChain` (taker path)

```solidity
function fillAsChain(
    uint256 marketId,
    Side side,
    uint256 maxPrice,
    uint256 amount,
    address taker,
    address recipient,
    uint256 maxFills
) external returns (uint256 filledAmount, uint256 totalCost);
```

**Role**: Public (dùng bởi Router trong routing path)
Walk qua orderbook đối diện tối đa `maxFills` iterations. Unused input trả lại `taker`.

## View functions

```solidity
function getOrder(bytes32 orderId) external view returns (Order memory);
function getBestPrices(uint256 marketId) external view
    returns (uint256 bestBidYes, uint256 bestAskYes, uint256 bestBidNo, uint256 bestAskNo);
function getDepthAtPrice(uint256 marketId, Side side, uint256 price) external view returns (uint256);
function getOrderBook(uint256 marketId, uint8 depth) external view
    returns (
        uint256[] memory yesBids, uint256[] memory yesAsks,
        uint256[] memory noBids,  uint256[] memory noAsks
    );
function getUserOrders(uint256 marketId, address user) external view returns (bytes32[] memory);
```

## Ràng buộc cứng

| Tham số | Giá trị |
|---|---|
| Price range | `10000`–`990000` (\$0.01–\$0.99) |
| Price step | `10000` (\$0.01) |
| Min order size | `1000000` (\$1) |
| Max orders per user per market | `50` |
| Max fills per `placeOrder` call | `20` |

## Pause semantics (asymmetric)

Module `MARKET` pause ảnh hưởng:

| Function | Pause? |
|---|---|
| `placeOrder` (maker path) | ✅ — block để tránh collateral mới lock |
| `fillAsChain` (taker path) | ❌ — **luôn open** để user thoát được vị thế |
| `cancelOrder` | ❌ — luôn open |

{% hint style="info" %}
**Pause asymmetry quan trọng cho MM**: nếu admin pause, maker không thể post order mới, nhưng bất kỳ ai cũng có thể fill order đã tồn tại (taker path). Điều này đảm bảo không ai bị "stuck" trong order.
{% endhint %}

## Phí CLOB

Khởi điểm 0% maker/taker (thu hút MM). Roadmap: tăng dần taker fee lên 1% từ month+7 sau launch.

Công thức fee (đối xứng):

```
fee = feeRate × min(price, 1 − price) × amount
```

Lý do: buy YES tại $0.7 và sell YES tại $0.3 về mặt rủi ro ngược nhau — fee dựa trên side "cheaper" đảm bảo fairness.

Khi CLOB match MINT (BUY_YES + BUY_NO, tổng > $1), phần thặng dư là doanh thu protocol. Xem [Matching Engine](../giao-dich/25-matching-engine.md).

## Events

```solidity
event OrderPlaced(
    bytes32 indexed orderId,
    uint256 indexed marketId,
    address indexed owner,
    Side side,
    uint256 price,
    uint256 amount
);

event OrderMatched(
    bytes32 indexed makerOrderId,
    bytes32 indexed takerOrderId,
    uint256 indexed marketId,
    MatchType matchType,
    uint256 amount,
    uint256 price
);

event OrderCancelled(bytes32 indexed orderId);
event FeeCollected(uint256 indexed marketId, uint256 amount);
```

Indexer reference: [Events Mapping](../indexer/03-events-mapping.md).
