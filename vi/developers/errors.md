---
description: Các lỗi phổ biến và cách khắc phục
---

# Tham chiếu lỗi

## Các lỗi phổ biến

| Lỗi | Nguyên nhân | Giải pháp |
| ----- | ----- | -------- |
| `Market_NotFound` | marketId không hợp lệ | Xác minh marketId từ sự kiện MarketCreated |
| `Market_Ended` | endTime của thị trường đã qua | Không thể giao dịch sau khi hết hạn |
| `Market_ExceedsPerTradeCap` | Vượt quá giới hạn an toàn mỗi giao dịch | Giảm kích thước giao dịch |
| `Market_ExceedsTvlCap` | Đạt giới hạn TVL toàn hệ thống | Chờ hoặc dùng số lượng nhỏ hơn |
| `Market_ExceedsPerMarketCap` | Đạt giới hạn tài sản thế chấp của thị trường | Dùng số lượng nhỏ hơn |
| `InvalidPrice` | Giá không nằm trên lưới tick $0.01 | Sử dụng bội số của `10000` (6 chữ số thập phân) |
| `MaxOrdersExceeded` | Người dùng có 50 lệnh trong thị trường này | Hủy các lệnh hiện có trước |
| `MarketNotActive` | Thị trường đã hết hạn, đã giải quyết hoặc đang ở chế độ hoàn tiền | Kiểm tra trạng thái thị trường |
| `InsufficientBalance` | Không đủ token cho thao tác | Kiểm tra số dư token |
| `SandwichDetected` | Hoán đổi ngược chiều trong cùng một khối | Chờ đến khối tiếp theo |
| `SlippageExceeded` | Đầu ra thấp hơn minOut | Tăng trượt giá hoặc thử lại |
| `DeadlineExpired` | Giao dịch được đào sau deadline | Đặt deadline dài hơn |
| `OnlyFactory` | Cố gắng mint/đốt token trực tiếp | Token chỉ có thể được mint qua Diamond |

## Mẹo gỡ lỗi

```typescript
try {
  await router.buyYes(marketId, usdcIn, minYesOut, recipient, deadline);
} catch (error: any) {
  // Giải mã lỗi tùy chỉnh
  const iface = new ethers.Interface(ROUTER_ABI);
  try {
    const decoded = iface.parseError(error.data);
    console.error("Lỗi tùy chỉnh:", decoded?.name, decoded?.args);
  } catch {
    console.error("Lỗi gốc:", error.message);
  }
}
```

---

**Tiếp theo**: [Hướng dẫn nhanh cho nhà phát triển](quickstart.md) · [Tích hợp giao dịch](trading-integration.md)
