# Prediction market là gì

Thị trường nơi người dùng mua bán *khả năng* một sự kiện xảy ra trong tương lai. Giá token phản ánh xác suất thị trường đánh giá, cập nhật realtime theo cung cầu.

## Ví dụ cụ thể

Sự kiện: *"Bitcoin vượt $100.000 trước 2027-01-01?"*

- Market tạo ra 2 token: **YES** và **NO**.
- Giá YES dao động trong khoảng $0 đến $1.
- Giá YES hiện tại = $0.68 → thị trường đang pricing sự kiện 68% khả năng xảy ra.
- Nếu sự kiện xảy ra: YES → $1, NO → $0. Ai giữ YES ăn lời, ai giữ NO mất toàn bộ.
- Nếu không xảy ra: ngược lại.

## Tại sao dùng cơ chế token

- **Giá là tín hiệu thông tin thật**. Ai tin sự kiện xảy ra sẽ mua YES, đẩy giá lên. Hợp nhiều ý kiến thành 1 con số.
- **Tiền thật skin-in-the-game** → tốt hơn khảo sát (người trả lời không bị mất gì nếu đoán sai).
- Nghiên cứu học thuật cho thấy prediction market thường chính xác hơn bình chọn chuyên gia ở các sự kiện có dữ liệu thống kê.

## PrediX khác gì với bookmaker truyền thống

| | Bookmaker (Bet365, 1xBet…) | Prediction market (PrediX, Polymarket) |
|---|---|---|
| Đối thủ đặt cược | House (sàn) | User khác |
| Giá | House đặt, spread rộng | Market-driven, AMM + CLOB |
| Lưu ký tiền | House giữ | Non-custodial (on-chain) |
| Có thể sell trước khi resolve | Khó / không | Có — bán lại token bất cứ lúc nào |
| Audit được không | Không | Có — on-chain transparent |

## Loại market trên PrediX

- **Binary** (YES/NO) — *"BTC có vượt $100k?"*. Phổ biến nhất.
- **Scalar** — *"GDP Việt Nam 2026 là bao nhiêu?"*. Long/Short với strike.
- **Multi-outcome event** — *"Ai thắng FIFA World Cup 2026?"* — mỗi đội là 1 market con, chỉ 1 thắng, tổng giá ≈ $1.
- **Sports** — pre-structured cho giải đấu.
- **Grouped** — nhóm market cùng theme, UI hiển thị chung.

Chi tiết cách multi-outcome hoạt động: [Event đa outcome](../huong-dan/event-multi-outcome.md).

## Giới hạn

- Market cần một **oracle** để report kết quả. Oracle có thể sai, dispute, hoặc chậm. Xem [Resolution & oracle](resolution.md).
- Thanh khoản phụ thuộc vào volume. Market không ai trade → spread rộng → slippage cao.
- Token YES/NO chỉ có giá trị trong context của 1 market cụ thể. Không dùng làm stablecoin.
