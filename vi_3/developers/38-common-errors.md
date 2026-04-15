# 38. Common Errors

| Error | Nguyên nhân | Cách fix |
|---|---|---|
| Market_NotFound | marketId không tồn tại | Kiểm tra lại marketId |
| Market_Ended | endTime đã qua | Không thể trade market hết hạn |
| Market_ExceedsPerTradeCap | Amount quá lớn | Chia nhỏ giao dịch |
| InvalidPrice | Giá không hợp lệ | Giá phải $0,01–$0,99, bước $0,01 |
| MaxOrdersExceeded | Đạt 50 lệnh/market | Huỷ lệnh cũ trước |
| MarketNotActive | Market expired/resolved/refund | Kiểm tra trạng thái market |
| InsufficientBalance | Không đủ token | Kiểm tra balance |
| SlippageExceeded | Giá thay đổi quá nhiều | Tăng slippage tolerance |


# PART VI: LIQUIDITY
