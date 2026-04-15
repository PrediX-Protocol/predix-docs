# 24. Smart Routing

## Cách Router Hoạt Động
Kiểm tra CLOB -có limit orders nào khớp được?
Kiểm tra AMM -giá AMM có tốt hơn?
Chia lệnh lớn qua cả hai venue
Ví dụ: Mua YES $100 → $60 khớp từ CLOB limit orders (giá tốt hơn) + $40 swap qua AMM.

## Khi Nào CLOB Tốt Hơn
Market có nhiều limit orders tại giá tốt
Size nhỏ khớp hết trong CLOB depth

## Khi Nào AMM Tốt Hơn
Market ít limit orders
Cần fill lớn ngay lập tức
