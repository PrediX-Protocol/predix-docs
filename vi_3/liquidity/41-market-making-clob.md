# 41. Market Making (CLOB)

## Chiến Lược
Đặt bid (mua) và ask (bán) orders cùng lúc, kiếm spread giữa hai. VD: Bid YES $0,49, Ask YES $0,51 → spread $0,02.

## Quản Lý Spread
Spread hẹp → nhiều fill, ít lợi nhuận mỗi fill
Spread rộng → ít fill, nhiều lợi nhuận mỗi fill
Điều chỉnh theo volatility và thời gian còn lại

## Inventory Risk
Nếu chỉ 1 bên fill, tích lũy vị thế. Cần rebalance thường xuyên. Max 50 orders/market/user.

# PART VII: SECURITY
