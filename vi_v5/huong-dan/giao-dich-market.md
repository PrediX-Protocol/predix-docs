# Lệnh thị trường (market order)

Mua hoặc bán ngay theo giá hiện tại. Router tự tìm path tốt nhất giữa CLOB và AMM.

## Khi nào dùng

- Bạn muốn vào vị thế **ngay**, chấp nhận giá thị trường.
- Bạn giao dịch nhỏ (< 1% liquidity), slippage chấp nhận được.
- Bạn không có view chờ giá.

Nếu muốn chờ giá tốt hơn → [Lệnh giới hạn](dat-lenh-limit.md).

## Bước — mua YES

1. Vào market, panel phải chọn tab **Buy**.
2. Chọn side **YES**.
3. Nhập số USDC chi (ví dụ: 100).
4. Đặt slippage tolerance (default 0.5%, có thể tăng nếu market ít liquidity).
5. Preview hiện:
   - Số YES nhận được (ước tính)
   - Giá trung bình
   - Slippage thực tế
   - Fee (AMM %, taker CLOB %)
   - Chia CLOB / AMM tỷ lệ bao nhiêu
6. Nhấn **Buy** → ví hỏi confirm.
7. Chờ ~2 giây, tx xuất hiện trong [Portfolio](portfolio.md) > History.

## Bán YES

Ngược lại:

1. Tab **Sell**, chọn YES, nhập số YES muốn bán.
2. Preview USDC nhận được.
3. Confirm.

Router sẽ:
- Drain bid orders CLOB trước (có người muốn mua YES).
- Swap AMM nếu CLOB không đủ.
- Đôi khi swap qua pool NO + synthetic path (mua NO từ USDC → merge với YES của bạn → nhận USDC).

## Mua / bán NO

Cơ chế đối xứng YES. Router có thể dùng **virtual-NO** trick — nếu pool NO-USDC thiếu thanh khoản, Router:

1. Ước lượng giá NO = $1 - giá YES.
2. Mua YES từ pool YES-USDC bằng USDC của bạn.
3. Split thêm USDC → mint NO riêng.
4. Merge YES mới mua với NO bạn đang bán → payout USDC cho bạn.

Bạn không cần care — UI chỉ hiện "Sell NO" và kết quả cuối.

## Slippage

Slippage = độ chênh giữa giá preview và giá thực tế khi tx execute.

- **Default 0.5%** phù hợp market liquid.
- Tăng lên **1–2%** nếu market có spread rộng.
- **> 5%**: hiếm khi nên, Router sẽ cảnh báo.

Nếu giá chạy quá slippage khi tx pending → tx **revert**, bạn không mất gì ngoài gas (miễn phí với smart account).

## Fee bạn trả

Hiển thị breakdown trên preview:

```
AMM swap fee:    0.50 USDC  (0.5% vì market còn > 7 ngày)
CLOB taker fee:  0.00 USDC  (0% trong kỳ bootstrap)
Gas:             Free (paymaster sponsor)
—————————
Tổng chi:        100.50 USDC
Nhận:            ~205 YES
```

Chi tiết phí: [Cấu trúc phí](../khai-niem/phi.md).

## Split / merge shortcut

Panel bên phải có tab **Split** và **Merge**:

- **Split 100 USDC** → nhận 100 YES + 100 NO. Dùng khi muốn hold cả 2 để sell riêng (market making).
- **Merge 100 YES + 100 NO** → nhận 100 USDC. Dùng khi đang có cả 2 và muốn rút.

Không phí protocol, chỉ gas (miễn phí với paymaster).

## Lỗi thường gặp

**"Slippage exceeded"** — Giá chạy quá tolerance. Tăng slippage hoặc chờ thị trường ổn định.

**"Insufficient liquidity"** — Cả CLOB và AMM không đủ depth. Giảm size hoặc dùng [limit order](dat-lenh-limit.md) chờ match.

**"Market paused"** — Admin pause market (bug, dispute). Xem thông báo trong UI.

**"Past endTime"** — Market đã đóng trading. Không mua bán được. Chờ resolve để redeem hoặc refund.
