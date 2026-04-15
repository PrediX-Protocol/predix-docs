# 33. Access Control & Safety

## 4 Roles

| Role | ID | Quyền |
|---|---|---|
| DEFAULT_ADMIN | 0 | Quản lý roles, toàn quyền |
| ADMIN | 1 | Tạo market, set oracle, set caps |
| OPERATOR | 2 | Emergency resolve, refund mode |
| PAUSER | 3 | Pause/unpause |


## Two-Step Ownership Transfer
Chuyển quyền sở hữu yêu cầu bước confirm. Last-admin protection ngăn xoá admin cuối cùng.

## Safety Caps
TVL Cap: Giới hạn tổng giá trị khoá trong protocol
Per-Trade Cap: Giới hạn size mỗi giao dịch
Per-Market Cap: Giới hạn collateral mỗi market

## Emergency Mechanisms
Pause: Dừng từng facet hoặc toàn bộ protocol
Emergency Resolve: Sau 7 ngày nếu oracle thất bại
Refund Mode: Hoàn USDC tỷ lệ khi không thể giải quyết
Reentrancy Guards: 3 loại trên tất cả external functions

# PART V: DEVELOPERS
