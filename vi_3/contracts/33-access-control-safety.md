---
description: Kiểm soát truy cập dựa trên vai trò — 4 vai trò với quyền chi tiết
---

# Access Control & Safety

## Các vai trò

| Vai trò        | ID | Mục đích                                       |
| -------------- | -- | ---------------------------------------------- |
| DEFAULT\_ADMIN | 0  | Quản lý tất cả các vai trò                     |
| ADMIN          | 1  | Tạo thị trường, cấu hình phí, phê duyệt Oracle |
| OPERATOR       | 2  | Xác nhận kết quả khẩn cấp, kích hoạt hoàn tiền |
| PAUSER         | 3  | Tạm dừng hệ thống                              |

## Ma trận quyền hạn

| Hàm                  | PUBLIC | ADMIN | OPERATOR   | PAUSER | OWNER |
| -------------------- | ------ | ----- | ---------- | ------ | ----- |
| `createMarket`       |        | ✓     |            |        |       |
| `splitPosition`      | ✓      |       |            |        |       |
| `mergePositions`     | ✓      |       |            |        |       |
| `resolveMarket`      | ✓      |       |            |        |       |
| `redeemMarketTokens` | ✓      |       |            |        |       |
| `placeOrder`         | ✓      |       |            |        |       |
| `buyYes` (Router)    | ✓      |       |            |        |       |
| `emergencyResolve`   |        |       | ✓ (7 ngày) |        |       |
| `enableRefundMode`   |        |       | ✓          |        |       |
| `setTvlCap`          |        | ✓     |            |        |       |
| `setApprovedOracle`  |        | ✓     |            |        |       |
| `pauseAll`           |        |       |            | ✓      |       |
| `diamondCut`         |        |       |            |        | ✓     |

## Chuyển quyền sở hữu hai bước

```
1. Chủ sở hữu hiện tại gọi transferOwnership(newOwner)
2. newOwner trở thành pendingOwner
3. newOwner gọi acceptOwnership()
4. Hoàn tất chuyển quyền sở hữu
```

Điều này ngăn chặn việc chuyển nhầm sang địa chỉ sai.

## Bảo vệ quản trị viên cuối cùng

Hệ thống ngăn chặn việc thu hồi vai trò của quản trị viên cuối cùng, đảm bảo giao thức luôn có ít nhất một quản trị viên.

