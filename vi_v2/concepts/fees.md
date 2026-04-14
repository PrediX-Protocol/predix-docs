# Phí

PrediX dùng mô hình phí lai giữa hai venue AMM và CLOB.

## Phí động AMM

Phí tăng dần khi market gần đến hạn, bù đắp cho LP trước rủi ro thông tin bất đối xứng lớn hơn.

| Thời gian còn lại | Phí | Lý do |
|----------------|----------|-----------|
| > 7 ngày | 0.5% | Phí thấp khuyến khích giao dịch sớm |
| 3–7 ngày | 1.0% | Phí trung bình, thông tin có giá trị hơn |
| 1–3 ngày | 2.0% | Rủi ro LP cao hơn |
| < 24 giờ | 5.0% | Bảo vệ tối đa gần giờ phân xử |

## Phí CLOB

| Loại | Tỷ lệ | Chi tiết |
|----------|------|---------|
| Taker fee | 0%–1% | Bắt đầu 0%, tăng theo thời gian |
| Surplus MINT | Thay đổi | Khi BUY_YES + BUY_NO > $1, phần dư là doanh thu giao thức |
| Surplus MERGE | Thay đổi | Khi SELL_YES + SELL_NO < $1, phần dư là doanh thu giao thức |

**Công thức phí (đối xứng):** `fee = feeRate × min(price, 1 − price) × quantity`

## Phí tạo market

Một khoản phí cố định bằng USDC có thể cấu hình ($10–$50) mỗi market, nhằm chống spam và tạo doanh thu giao thức.

## Ví dụ tính phí

Mua 100 YES với giá $0.65 qua AMM, market còn 5 ngày:

```
Phí áp dụng: 1.0% (khung 3–7 ngày)
Phí: 100 × $0.65 × 1.0% = $0.65
Tổng chi phí: $65.00 + $0.65 = $65.65
```

## Tiếp theo

- [Tổng quan giao dịch](../trading/overview.md) — cách lệnh được route
- [Thanh khoản](../liquidity/overview.md) — cách LP kiếm phí
- [Smart Routing](../trading/smart-routing.md) — phí ảnh hưởng đến routing
