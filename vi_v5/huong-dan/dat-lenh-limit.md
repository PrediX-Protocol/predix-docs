# Lệnh giới hạn (CLOB)

Đặt giá chờ khớp. Bạn là maker — phí 0%, có price improvement.

## Khi nào dùng

- Bạn có view giá — muốn mua YES ở $0.45 mà thị trường đang $0.50, chờ giảm.
- Bạn market-make — đặt BUY và SELL cùng lúc, ăn spread.
- Bạn giao dịch lớn, tránh slippage qua AMM.

## Bước — đặt lệnh mua YES giá $0.45

1. Market panel, chọn tab **Limit**.
2. Side **YES**, direction **Buy**.
3. Price: `0.45`.
4. Amount: `100 USDC` (hoặc số YES, UI cho convert).
5. Preview hiện:
   - Nếu fill: nhận `100 / 0.45 ≈ 222.22 YES`.
   - USDC bị lock: 100 USDC.
6. Nhấn **Place Order** → ví confirm.
7. Lệnh xuất hiện trong tab **Open Orders** ở Portfolio.

## Tick size

Giá chỉ chấp nhận bội số $0.01 (1% của $1): `0.01, 0.02, 0.03, ..., 0.99`.
- `0.455` sẽ bị làm tròn hoặc reject.
- 99 tick tổng, lưu trên bitmap nén ở contract `PrediXExchange`.

Không hỗ trợ `0.00` hoặc `1.00` — giá = certainty, không trade được.

## 3 loại match

Khi ai đó đặt market order hoặc limit order đối lập, Exchange match theo 3 cách:

### 1. Complementary (phổ biến nhất)

Bạn: BUY YES @ $0.45 100 USDC.
Ai đó đặt: SELL YES @ $0.44 50 USDC.

Match direct: taker bán YES cho bạn @ $0.44 (giá tốt hơn bạn đặt). Bạn nhận `50 / 0.44 ≈ 113.6 YES`, còn dư 50 USDC lock chờ tiếp.

### 2. Mint (synthetic)

Bạn: BUY YES @ $0.60.
Ai đó: BUY NO @ $0.45. Tổng: $1.05 ≥ $1.

Exchange mint 1 cặp YES+NO từ $1 USDC. Đưa YES cho bạn, NO cho người kia. Spread $0.05 → protocol.

Kết quả: cả 2 bên đều được token muốn, protocol thu spread, supply pool tăng.

### 3. Merge (synthetic)

Bạn: SELL YES @ $0.40.
Ai đó: SELL NO @ $0.45. Tổng: $0.85 ≤ $1.

Exchange gom YES của bạn + NO của kia, burn → nhận $1 USDC. Trả $0.40 cho bạn + $0.45 cho kia. Spread $0.15 → protocol.

Bạn không cần tính toán — matching engine tự tối ưu. UI hiển thị matchType trong History.

## Hủy lệnh

1. Vào [Portfolio](portfolio.md) > **Open Orders**.
2. Tìm lệnh, nhấn **Cancel**.
3. Ví confirm → USDC/token lock được release về ví trong tx.

Miễn phí protocol, miễn phí gas qua paymaster.

## Lệnh partial fill

Lệnh của bạn có thể bị fill một phần:

- BUY YES @ $0.45 100 USDC → ai đó ăn 40 USDC → còn 60 USDC lock trên sổ.
- Status chuyển từ `OPEN` → `PENDING` (theo indexer).
- Còn lại tiếp tục chờ khớp, hoặc bạn cancel phần dư.

## Giới hạn

- **80 lệnh open/user/market**: chống spam sổ lệnh.
- **Min size**: $1 USDC (tương đương `1_000_000` base unit) để tránh dust.
- Không hỗ trợ **stop-loss** hay **OCO** — limit đơn thuần. (Roadmap Phase 2.)

## Tips

- Đặt maker BUY và SELL quanh mid-price → ăn spread, làm market maker thụ động.
- Dùng tiered orders: $0.40 50 USDC, $0.42 50 USDC, $0.44 50 USDC → scale-in thay vì all-in.
- Kiểm tra **order book depth** trước — nếu best bid/ask cách mid > 5%, thị trường thanh khoản yếu, bạn có thể là người đầu tiên ở tick mới.

## Giá tốt hơn taker

Khi match complementary, taker trả phí, maker không. Nếu lệnh của bạn được match ngay trong cùng block với taker đặt ngược phía, **bạn hưởng price improvement** — taker ăn đúng giá bạn đặt, không phải mid.

Đây là lý do maker CLOB thường giá tốt hơn swap AMM.
