# Giao dịch đầu tiên

Mua YES trên một market bất kỳ. ~30 giây.

## Bước

1. Vào trang [Markets](https://app.predix.app/markets), chọn một market.
2. Ở panel bên phải, chọn **YES** hoặc **NO**.
3. Nhập số USDC muốn chi (ví dụ: 10 USDC).
4. Xem preview: số token YES nhận được, giá trung bình, slippage ước tính.
5. Nhấn **Buy** → xác nhận trên ví (passkey / MetaMask).
6. Chờ tx confirm (~2 giây trên Unichain).

Vị thế xuất hiện ở [Portfolio](../huong-dan/portfolio.md) ngay sau khi tx được index (trễ < 10 giây).

## Hiểu gì đang xảy ra

Khi bạn chi 10 USDC để mua YES:

- **Router** kiểm tra sổ lệnh CLOB trước. Nếu có ai đặt bán YES giá tốt, match trực tiếp.
- Nếu CLOB không đủ thanh khoản, phần còn lại swap qua **Uniswap v4 pool** (YES-USDC hoặc NO-USDC).
- Một số trường hợp, Router mint cặp YES+NO từ USDC và bán NO cho maker → bạn vẫn nhận YES.
- Tất cả xảy ra atomic trong 1 tx. Nếu slippage vượt `minOut` bạn đặt, tx revert, tiền không mất.

Chi tiết cơ chế: [CLOB và AMM lai](../khai-niem/clob-va-amm.md).

## Các loại lệnh

| Loại | Khi nào dùng | Phí |
|---|---|---|
| **Market (instant)** | Mua/bán ngay theo giá thị trường | Dynamic 0.5–5% tuỳ thời gian tới endTime |
| **Limit (CLOB)** | Đặt giá chờ khớp — như sổ lệnh CEX | 0% maker, 0–1% taker (kích hoạt sau Mainnet + 7) |
| **Split** | Chia 1 USDC → 1 YES + 1 NO, bán riêng từng token | Không phí split, chỉ gas |
| **Merge** | Gộp 1 YES + 1 NO → 1 USDC (nếu cả hai bạn đang giữ) | Không phí |

Cách dùng limit order: [Lệnh giới hạn](../huong-dan/dat-lenh-limit.md).

## Ví dụ

Market: *"Bitcoin có vượt $100k trước 2027-01-01?"*. Giá YES hiện = $0.48.

Bạn chi 10 USDC:
- CLOB có maker bán YES giá $0.48 với volume 5 USDC → match 5 USDC, nhận 10.41 YES.
- Còn 5 USDC → Router swap qua AMM. Giá trung bình $0.485 → nhận 10.30 YES.
- **Tổng**: 20.71 YES, giá trung bình ~$0.4829, slippage 0.6%.

Nếu sự kiện xảy ra (BTC vượt $100k):
- Market resolve YES = true.
- Bạn redeem 20.71 YES → nhận 20.71 USDC (trừ fee redemption nếu có).

Nếu không xảy ra:
- YES tokens của bạn = $0. Kết quả: lỗ 10 USDC.

## Kiểm tra số dư

- Token YES/NO là ERC-20. Thêm address vào ví để thấy balance.
- Balance YES/NO mỗi market xem ở [Portfolio](../huong-dan/portfolio.md) — hiển thị value dựa trên giá spot hiện tại.
