# 20. Phí Giao Dịch

## AMM Dynamic Fees

Phí tăng dần khi market gần hết hạn để bảo vệ LP:

<table><thead><tr><th>Thời gian còn lại</th><th width="249">Phí</th><th>Lý do</th></tr></thead><tbody><tr><td>> 7 ngày</td><td>0,5%</td><td>Khuyến khích giao dịch sớm</td></tr><tr><td>3–7 ngày</td><td>1,0%</td><td>Thông tin có giá trị hơn</td></tr><tr><td>1–3 ngày</td><td>2,0%</td><td>Toxic flow tăng</td></tr><tr><td>&#x3C; 24 giờ</td><td>5,0%</td><td>Adverse selection đỉnh điểm</td></tr></tbody></table>

### CLOB Fees

Bắt đầu 0% (thu hút market makers). Tăng lên 1% taker từ M+7.&#x20;

Công thức đối xứng: fee = feeRate × min(price, 1−price) × amount.

### MINT/MERGE Surplus

Khi CLOB khớp MINT (BUY\_YES + BUY\_NO, tổng > $1), phần thặng dư là doanh thu protocol. VD: $0,72 + $0,30 = $1,02, thặng dư $0,02.
