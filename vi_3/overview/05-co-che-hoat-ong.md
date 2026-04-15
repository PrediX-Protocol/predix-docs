# 5. Cơ Chế Hoạt Động

## 5.1 Cơ Chế Giao Dịch
Người dùng trade outcome tokens bằng USDC. Mỗi market có các token đại diện cho từng kết quả -với market đơn giản là YES/NO, với multi-outcome (Categories) là token cho mỗi lựa chọn. Chỉ YES có pool AMM trực tiếp (YES/USDC). Token NO được định giá ảo:
Mua NO: Split USDC → YES+NO, bán YES trên AMM, giữ NO
Bán NO: Mua YES trên AMM, merge YES+NO → USDC
Thao tác nguyên tử qua flash accounting Uniswap v4
Quan hệ giá: NO ≈ 1 − YES, duy trì bởi arbitrage

## 5.2 Smart Router
Router kiểm tra CLOB trước → AMM → chia lệnh lớn qua cả hai. Ví dụ: mua YES $100 → $60 từ CLOB + $40 từ AMM. Quote functions cho phép preview.

## 5.3 CLOB Matching Engine
Ba chế độ khớp lệnh:
COMPLEMENTARY: BUY_YES ↔ SELL_YES trực tiếp
MINT: BUY_YES + BUY_NO khi tổng ≥ $1,00. Thặng dư = doanh thu protocol
MERGE: SELL_YES + SELL_NO khi tổng ≤ $1,00. Merge → USDC
Cải thiện giá: mua tại $0,70, khớp tại $0,50 → hoàn $0,20. 99-tick bitmap, bước $0,01. Min $1,00. Max 50 lệnh/user/market.

## 5.4 Lịch Phí Dynamic

| Thời gian còn lại | Phí AMM | Lý do |
|---|---|---|
| > 7 ngày | 0,5% | Khuyến khích giao dịch sớm |
| 3–7 ngày | 1,0% | Thông tin có giá trị hơn |
| 1–3 ngày | 2,0% | Bù đắp LP về toxic flow |
| < 24 giờ | 5,0% | Bảo vệ LP gần expiry |

CLOB: 0% ban đầu → 1% taker. Công thức: fee = feeRate × min(giá, 1−giá) × số lượng.

## 5.5 Split & Merge
Split: $1 USDC → 1 YES + 1 NO
Merge: 1 YES + 1 NO → $1 USDC
Neo bất biến YES + NO ≈ $1, tạo arbitrage giữ giá

## 5.6 Vòng Đời Market
Tạo → Hoạt động → Hết hạn → Đã giải quyết → Có thể đổi
Giải quyết thường: Oracle resolve → bất kỳ ai gọi resolveMarket()
Giải quyết khẩn cấp: OPERATOR resolve 7 ngày sau endTime nếu oracle thất bại
Hoàn tiền: USDC theo tỷ lệ nếu market không thể giải quyết

## 5.7 Multi-Outcome Markets (Categories)
Categories nhóm các market liên quan thành một sự kiện multi-outcome (VD: “Tổng thống Mỹ 2028” với 1 market/ứng viên). GroupSplit/GroupMerge cho batch operations. resolveCategory(categoryId, winningIndex) giải quyết toàn category.
