# 19. Resolution (Giải Quyết Market)

## Vòng Đời
Created → Active → Expired → Resolved → Redeemable

## Giải Quyết Thường
Oracle giải quyết kết quả (resolve)
Bất kỳ ai gọi resolveMarket(marketId)
Token thắng = $1, token thua = $0
Users gọi redeemMarketTokens(marketId) để đổi USDC

## Giải Quyết Khẩn Cấp
Nếu oracle thất bại, OPERATOR có thể gọi emergencyResolve() sau 7 ngày kể từ endTime.

## Chế Độ Hoàn Tiền (Refund Mode)
Khi market không thể giải quyết đúng cách, OPERATOR kích hoạt enableRefundMode(). Users gọi refund() để nhận USDC theo tỷ lệ token đang giữ.
