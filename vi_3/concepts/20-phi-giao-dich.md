# 20. Phí Giao Dịch

## AMM Dynamic Fees
Phí tăng dần khi market gần hết hạn để bảo vệ LP:

| Thời gian còn lại | Phí | Lý do |
|---|---|---|
| > 7 ngày | 0,5% | Khuyến khích giao dịch sớm |
| 3–7 ngày | 1,0% | Thông tin có giá trị hơn |
| 1–3 ngày | 2,0% | Toxic flow tăng |
| < 24 giờ | 5,0% | Adverse selection đỉnh điểm |


## CLOB Fees
Bắt đầu 0% (thu hút market makers). Tăng lên 1% taker từ M+7. Công thức đối xứng: fee = feeRate × min(price, 1−price) × amount.

## MINT/MERGE Surplus
Khi CLOB khớp MINT (BUY_YES + BUY_NO, tổng > $1), phần thặng dư là doanh thu protocol. VD: $0,72 + $0,30 = $1,02, thặng dư $0,02.

# PART III: TRADING
