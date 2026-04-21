# 25. Matching Engine

CLOB của PrediX có **đúng 3 MatchType** được định nghĩa on-chain (enum `MatchType` trong `IPrediXExchange`):

| # | MatchType | Giải thích |
|---|---|---|
| 0 | `COMPLEMENTARY` | Maker + taker đối diện nhau trên cùng side (BUY_YES ↔ SELL_YES, BUY_NO ↔ SELL_NO) |
| 1 | `MINT` | Hai lệnh cùng mua (BUY_YES + BUY_NO), tổng ≥ $1 → Diamond mint cặp mới |
| 2 | `MERGE` | Hai lệnh cùng bán (SELL_YES + SELL_NO), tổng ≤ $1 → Diamond burn cặp |

Ngoài 3 MatchType, có một **price improvement rule** (không phải MatchType thứ 4) — xem cuối trang.

---

## 🤝 MatchType 0 — COMPLEMENTARY

Trường hợp đơn giản: có người muốn mua YES, có người muốn bán YES → trao đổi trực tiếp.

```
Người mua YES  →  trả USDC  →  nhận token YES
Người bán YES  →  trả token YES  →  nhận USDC
```

Tương tự cho side NO. Không có mint/burn.

---

## 🪙 MatchType 1 — MINT

Xảy ra khi có người mua YES **và** người mua NO cùng lúc, với tổng giá **≥ $1.00**.

Protocol gom USDC của cả hai, gọi `Diamond.splitPosition` để đúc cặp YES + NO mới, phát cho mỗi người phần họ muốn.

**Ví dụ:**

- Person A đặt lệnh BUY_YES @ $0.72 (100 tokens)
- Person B đặt lệnh BUY_NO @ $0.30 (100 tokens)
- Tổng: $1.02 → Protocol mint 100 cặp mới từ $1.00 × 100 = $100, phần thặng dư `$0.02 × 100 = $2` vào fee recipient

> 💡 Cơ chế này giúp tạo thanh khoản **mới** khi thị trường chưa có sẵn token để giao dịch.

---

## 🔥 MatchType 2 — MERGE

Ngược lại với MINT: người bán YES + người bán NO cùng lúc, tổng giá **≤ $1.00**.

Protocol gom cặp YES + NO, gọi `Diamond.mergePositions` để burn, trả USDC về cho cả hai.

**Ví dụ:**

- Person A đặt lệnh SELL_YES @ $0.50 (100 tokens)
- Person B đặt lệnh SELL_NO @ $0.45 (100 tokens)
- Tổng: $0.95 → Protocol merge 100 cặp → $100 USDC, chia cho A + B theo tỷ lệ giá (A nhận $50, B nhận $45), thiếu hụt $5 là chi phí exit.

> 💡 Cơ chế này cho phép user thoát vị thế ngay cả khi không có người mua trực tiếp ở phía đối diện.

---

## 🎁 Price Improvement Rule (không phải MatchType riêng)

Hệ thống luôn ưu tiên khớp lệnh của bạn ở **mức giá có lợi nhất**, không nhất thiết đúng mức giá bạn đặt. Rule này áp dụng cho cả 3 MatchType.

**Ví dụ:**

- Bạn đặt BUY_YES @ $0.70 (chấp nhận trả tối đa $0.70)
- Thị trường có SELL_YES @ $0.50
- System match tại $0.50 → **hoàn lại $0.20** cho bạn

```
Bạn sẵn sàng trả:   $0.70
Thực tế khớp tại:   $0.50
Bạn được hoàn:      $0.20  ✅
```

> ⚡ Đặt lệnh limit **không bao giờ** khiến bạn trả đắt hơn mức đồng ý — chỉ có thể rẻ hơn (hoặc bằng).

---

## Thứ tự ưu tiên

Khi `placeOrder` vào orderbook, contract match theo:

1. **Price-time priority**: best price first, FIFO nếu cùng price
2. **COMPLEMENTARY** ưu tiên xét trước (không tạo/đốt token)
3. **MINT/MERGE** xét sau — khi không có counterparty complementary

Max `maxFills = 20` iterations per `placeOrder` call để giới hạn gas.

---

## Events phát ra

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
    MatchType matchType,   // 0=COMPLEMENTARY | 1=MINT | 2=MERGE
    uint256 amount,
    uint256 price
);
```

Chi tiết: [PrediXExchange reference](../smart-contracts/29-exchange-clob.md).
