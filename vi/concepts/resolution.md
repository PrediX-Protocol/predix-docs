---
description: Cách thị trường được xác định kết quả và cách người thắng đổi thưởng
---

# Xác định kết quả

## Vòng đời thị trường

```
Đã tạo → Hoạt động (giao dịch) → Hết hạn → Đã xác định → Có thể đổi thưởng
                                    ↓
                            Khẩn cấp (7 ngày) → Xác định khẩn cấp
                                    ↓
                            Chế độ hoàn tiền → Người dùng hoàn tiền
```

## Xác định kết quả thông thường

1. Thị trường đạt `endTime` (giao dịch tiếp tục nhưng không tách mới)
2. Oracle báo cáo kết quả: `resolve(marketId, true/false)`
3. Bất kỳ ai gọi `resolveMarket(marketId)` trên Diamond
4. Thị trường được đánh dấu đã xác định
5. Người thắng gọi `redeemMarketTokens(marketId)`

```typescript
// Bước 1: Xác định kết quả (bất kỳ ai có thể gọi sau khi oracle báo cáo)
await diamond.resolveMarket(marketId);

// Bước 2: Đổi token thắng
await diamond.redeemMarketTokens(marketId);
// Người thắng nhận 1 USDC cho mỗi token thắng
```

## Xác định kết quả khẩn cấp

Nếu oracle không báo cáo trong vòng 7 ngày sau `endTime`:

1. `OPERATOR` có thể gọi `emergencyResolve(marketId, outcome)`
2. Bỏ qua oracle và đặt kết quả trực tiếp

> ⚠️ Xác định khẩn cấp yêu cầu **trì hoãn 7 ngày** sau `endTime` và chỉ có thể được gọi bởi vai trò OPERATOR.

## Chế độ hoàn tiền

Nếu thị trường được coi là không hợp lệ hoặc có vấn đề:

1. `OPERATOR` gọi `enableRefundMode(marketId)`
2. Tất cả giao dịch bị chặn (tách, gộp, CLOB, AMM)
3. Người giữ token gọi `refund(marketId)`
4. Mỗi người giữ nhận USDC theo tỷ lệ số dư YES + NO

```typescript
// Người vận hành kích hoạt hoàn tiền
await diamond.enableRefundMode(marketId);

// Người dùng yêu cầu hoàn tiền theo tỷ lệ
await diamond.refund(marketId);
```

## Xác định kết quả danh mục

Đối với thị trường đa kết quả:

```typescript
// Quản trị viên xác định danh mục: người thắng là chỉ số kết quả 2
await diamond.resolveCategory(categoryId, 2);
// Tất cả thị trường trong danh mục được xác định nguyên tử
```

---

**Tiếp theo**: [Thị trường đa kết quả](multi-outcome-markets.md) · [Phí](fees.md) · [Hợp đồng Oracle](../contracts/oracle.md)
