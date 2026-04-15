# 25. Matching Engine

## COMPLEMENTARY

BUY_YES ↔ SELL_YES trao đổi trực tiếp. Buyer trả USDC, nhận YES. Seller trả YES, nhận USDC.

## MINT

BUY_YES + BUY_NO khớp khi tổng giá ≥ $1,00. Protocol mint outcome tokens từ USDC cả hai bên. Thặng dư (VD: $0,72 + $0,30 = $1,02 → $0,02) = doanh thu protocol.

## MERGE

SELL_YES + SELL_NO khớp khi tổng giá ≤ $1,00. Outcome tokens merge lại thành USDC. Chi phí (VD: $0,50 + $0,45 = $0,95 → $0,05 shortfall) = discount cho sellers.

## Price Improvement

Buyer đặt limit $0,70, khớp tại $0,50 → hoàn $0,20. Luôn khớp tại giá có lợi nhất cho buyer.
