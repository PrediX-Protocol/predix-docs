# Cơ Chế Hoạt Động

### 1 Cơ Chế Giao Dịch

Người dùng trade outcome tokens bằng USDC. Mỗi market có các token đại diện cho từng kết quả với market đơn giản là YES/NO, với multi-outcome (Categories) là token cho mỗi lựa chọn. Chỉ YES có pool AMM trực tiếp (YES/USDC). Token NO được định giá dựa vào giá YES :

* **Mua NO:** Split USDC → YES+NO, bán YES trên AMM, giữ NO
* **Bán NO:** Mua YES trên AMM, merge YES+NO → USDC
* Thao tác nguyên tử qua flash accounting Uniswap v4
* **Quan hệ giá:** NO ≈ 1 − YES, duy trì bởi arbitrage

### 2 Smart Router

Router kiểm tra CLOB trước → AMM → chia lệnh lớn qua cả hai.&#x20;

Ví dụ: mua YES $100 → $60 từ CLOB + $40 từ AMM. Quote functions cho phép preview.

### 3 CLOB Matching Engine

Ba chế độ khớp lệnh:

* **COMPLEMENTARY:** BUY\_YES ↔ SELL\_YES trực tiếp
* **MINT:** BUY\_YES + BUY\_NO khi tổng ≥ $1,00. Thặng dư = doanh thu protocol
* **MERGE:** SELL\_YES + SELL\_NO khi tổng ≤ $1,00. Merge → USDC
* **Cải thiện giá:** mua tại $0,70, khớp tại $0,50 → hoàn $0,20. 99-tick bitmap, bước $0,01. Min $1,00. Max 50 lệnh/user/market.

### 4 Lịch Phí Dynamic

<table><thead><tr><th width="174.46484375">Thời gian còn lại</th><th width="157.30078125">Phí AMM</th><th>Lý do</th></tr></thead><tbody><tr><td>> 7 ngày</td><td>0,5%</td><td>Khuyến khích giao dịch sớm</td></tr><tr><td>3–7 ngày</td><td>1,0%</td><td>Thông tin có giá trị hơn</td></tr><tr><td>1–3 ngày</td><td>2,0%</td><td>Bù đắp LP về toxic flow</td></tr><tr><td>&#x3C; 24 giờ</td><td>5,0%</td><td>Bảo vệ LP gần expiry</td></tr></tbody></table>

CLOB: 0% ban đầu → 1% taker. Công thức: fee = feeRate × min(giá, 1−giá) × số lượng.

### 5 Split & Merge

* **Split:** $1 USDC → 1 YES + 1 NO
* **Merge:** 1 YES + 1 NO → $1 USDC
* Neo bất biến YES + NO ≈ $1, tạo arbitrage giữ giá

### 6 Vòng Đời Market

* Tạo → Hoạt động → Hết hạn → Đã giải quyết → Có thể đổi
* **Giải quyết thường:** Oracle resolve → bất kỳ ai gọi resolveMarket()
* **Giải quyết khẩn cấp:** OPERATOR resolve 7 ngày sau endTime nếu oracle thất bại
* **Hoàn tiền:** USDC theo tỷ lệ nếu market không thể giải quyết

### 7 Multi-Outcome Markets (Categories)

Categories nhóm các market liên quan thành một sự kiện multi-outcome (VD: “Tổng thống Mỹ 2028” với 1 market/ứng viên). GroupSplit/GroupMerge cho batch operations. resolveCategory(categoryId, winningIndex) giải quyết toàn category.
