# Limit order (CLOB)

Đặt giá chờ khớp trên on-chain order book. Bạn là **maker** — fee 0%, có price improvement.

## Khi nào dùng

- Có view giá — muốn buy YES @ $0.45 mà thị trường đang $0.50, chờ giá giảm.
- Market-make: đặt BUY và SELL cùng lúc, ăn spread.
- Trade lớn, tránh slippage qua AMM.

## Bước — đặt buy YES @ $0.45

1. Market panel, chọn tab **Limit**.
2. Side **YES**, direction **Buy**.
3. Price: `0.45`.
4. Amount: `100 USDC` (UI cho convert sang YES).
5. Preview:
   - Nếu fill: nhận `100 / 0.45 ≈ 222.22 YES`.
   - USDC bị lock: 100 USDC (release khi cancel hoặc match).
6. Click **Place Order** → ví confirm.
7. Lệnh xuất hiện ở [Portfolio](portfolio.md) → **Open Orders**.

## Tick size

Giá chỉ chấp nhận bội số $0.01: `0.01, 0.02, …, 0.99`.

- `0.455` sẽ bị reject hoặc làm tròn.
- 99 tick total, lưu trên bitmap nén ở contract `PrediXExchange`.

Không hỗ trợ `0.00` hoặc `1.00` — giá = certainty, không có thanh khoản.

## 3 loại match

Khi ai đó đặt market order hoặc limit đối lập, Exchange match theo 3 cách:

### 1. Complementary (phổ biến nhất)

```
Bạn:    BUY YES @ $0.45 — 100 USDC
Khác:   SELL YES @ $0.44 — 50 USDC
Match:  Taker bán YES cho bạn @ $0.44 (giá tốt hơn bạn đặt)
        Bạn nhận 50 / 0.44 ≈ 113.64 YES
        Còn dư 50 USDC lock chờ tiếp
```

### 2. Mint (synthetic)

```
Bạn:    BUY YES @ $0.60
Khác:   BUY NO @ $0.45
Tổng:   $1.05 ≥ $1
Action: Diamond mint cặp YES+NO từ $1 USDC
        Đưa YES cho bạn, NO cho khác
        Spread $0.05 → protocol
```

Cả 2 đều được token muốn, protocol thu spread.

### 3. Merge (synthetic)

```
Bạn:    SELL YES @ $0.40
Khác:   SELL NO @ $0.45
Tổng:   $0.85 ≤ $1
Action: Diamond gom YES + NO, burn → $1 USDC
        Trả $0.40 cho bạn + $0.45 cho khác
        Spread $0.15 → protocol
```

Bạn không cần tính toán — matching engine tự tối ưu. UI hiển thị `matchType` trong History.

## Cancel lệnh

1. [Portfolio](portfolio.md) → **Open Orders**.
2. Tìm lệnh, click **Cancel**.
3. Ví confirm → USDC / token lock release về ví trong cùng tx.

Free phí protocol. Gas mặc định user trả; sponsor cover nếu đủ điều kiện chương trình (áp dụng cả 2 account types).

## Partial fill

Lệnh có thể fill từng phần:

- BUY YES @ $0.45, 100 USDC → ai đó ăn 40 USDC → còn 60 USDC lock trên sổ.
- Status: `OPEN` → `PENDING` (theo indexer).
- Phần còn lại tiếp tục chờ khớp, hoặc bạn cancel.

## Giới hạn

| | Value |
|---|---|
| Max open orders / user / market | 80 |
| Min size | $1 USDC |
| Tick size | $0.01 |
| Stop-loss / OCO | TBA Phase 2 |

## Tips market-making

- **Đặt 2 phía** quanh mid-price → ăn spread, làm market maker thụ động.
- **Tiered orders**: $0.40 50 USDC, $0.42 50 USDC, $0.44 50 USDC → scale-in thay vì all-in.
- **Kiểm tra order book depth** trước khi đặt — nếu best bid/ask cách mid > 5% nghĩa là thanh khoản yếu, lệnh của bạn có thể là tick mới.
- **Chú ý endTime**: Limit order tự cancel khi market resolve/end. Reset lại mỗi market mới.
- **Cân nhắc trở thành LP** thay vì limit order pure — LP có exposure giá nhưng earn fee passive cả 2 chiều. Xem [Liquidity provider](cung-cap-thanh-khoan.md).

## Maker vs taker — vì sao maker được giá tốt hơn

Khi match complementary, taker trả fee, maker không. Nếu lệnh của bạn match ngay trong cùng block với taker đặt ngược phía:

- Taker ăn đúng giá bạn đặt, không phải mid.
- Bạn (maker) hưởng **price improvement** — giá tốt hơn AMM spot.

Đây là lý do maker CLOB thường có giá tốt hơn so với swap AMM thông thường.

## Watch your orders

- App có notification khi:
  - Order fill (full hoặc partial)
  - Order cancel (do bạn hoặc do market resolve)
  - Order gần endTime (warning để cân nhắc cancel)
- Webhook / API tích hợp bot — xem [Developers](../developers/bots-api.md).
