---
description: Giới hạn an toàn, kiểm soát khẩn cấp, bảo vệ tái nhập và chống tấn công sandwich
---

# Cơ chế an toàn

## Giới hạn an toàn

| Giới hạn | Mô tả | Quyền cấu hình |
| --- | ----------- | --------------- |
| Giới hạn TVL | USDC tối đa trong hệ thống | ADMIN |
| Giới hạn mỗi giao dịch | USDC tối đa mỗi lần tách/giao dịch | ADMIN |
| Giới hạn mỗi thị trường | USDC tối đa mỗi thị trường | ADMIN |

Các giới hạn bắt đầu thận trọng và có thể tăng khi giao thức trưởng thành.

## Kiểm soát khẩn cấp

| Cơ chế | Vai trò | Mô tả |
| --------- | ---- | ----------- |
| Tạm dừng module | PAUSER | Đóng băng module thị trường cụ thể |
| Tạm dừng toàn bộ | PAUSER | Đóng băng toàn bộ hệ thống |
| Xác nhận kết quả khẩn cấp | OPERATOR | Xác nhận kết quả sau 7 ngày trì hoãn (oracle lỗi) |
| Chế độ hoàn tiền | OPERATOR | Hoàn tiền theo tỷ lệ cho tất cả người nắm giữ |

## Bảo vệ tái nhập

Ba bộ bảo vệ độc lập bảo vệ các ranh giới hợp đồng khác nhau:

1. **Bảo vệ lưu trữ Diamond**: Bảo vệ các hàm MarketFacet
2. **Lưu trữ tạm thời Exchange/Router**: Bảo vệ CLOB và định tuyến
3. **Bảo vệ trạng thái Hook**: Bảo vệ callback Uniswap v4 Hook

20 hàm được bảo vệ bằng modifier `nonReentrant`.

## Chống tấn công Sandwich

Được tích hợp vào Hook ở cấp giao thức. Phát hiện các swap ngược chiều của cùng một người dùng trong cùng một block.

Lớp bổ sung: Unichain cung cấp bảo vệ hoàn lại ở cấp bộ tuần tự.

## Bất biến

```
1. YES.totalSupply == NO.totalSupply == totalCollateral (luôn luôn)
2. Split(N) + Merge(N) = đồng nhất (không rò rỉ giá trị)
3. Tổng đổi thưởng ≤ tổng nạp
4. Số dư Exchange ≥ tổng tiền gửi lệnh chờ
5. Chế độ hoàn tiền chặn TẤT CẢ giao dịch (tách, gộp, CLOB, AMM)
```

---

**Tiếp theo**: [Tổng quan bảo mật](../security/overview.md) · [Kiểm soát truy cập](access-control.md)
