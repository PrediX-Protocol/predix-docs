# 39. Liquidity Overview

## Hai Loại Cung Cấp Thanh Khoản
AMM LP (Uniswap v4): Cung cấp YES/USDC vào pool, kiếm swap fees (0,5–5% dynamic)
CLOB Orders: Đặt limit orders bid+ask, kiếm spread

## LP Kiếm Phí Như Thế Nào
AMM LP nhận phí từ mỗi swap qua pool. Phí dynamic 0,5–5% tùy thời gian còn lại. LP sớm nhận phí thấp nhưng ít rủi ro, LP gần expiry nhận phí cao nhưng toxic flow lớn.

## Rủi Ro
Impermanent loss: Giá YES thay đổi mạnh → IL
Toxic flow: Gần expiry, informed traders extract value từ LP
Market resolve: Nếu LP chưa rút trước resolve, 1 side = $0

## Best Practices
Rút LP trước khi market gần hết hạn (> 24h trước)
Concentrated liquidity quanh giá hiện tại
Theo dõi dynamic fees để đánh giá risk/reward
