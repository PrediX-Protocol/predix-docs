---
description: Giải quyết thị trường và đổi thưởng token thắng
---

# Giải quyết & Đổi thưởng

## Giải quyết thị trường

Sau khi oracle báo cáo kết quả, bất kỳ ai cũng có thể xác nhận:

```typescript
// Kiểm tra xem oracle đã báo cáo chưa
const oracle = new ethers.Contract(ORACLE_ADDRESS, ORACLE_ABI, provider);
const [resolved, outcome] = await oracle.getResolution(marketId);

if (resolved) {
  const tx = await diamond.resolveMarket(marketId);
  await tx.wait();
  console.log("Thị trường đã giải quyết. Kết quả:", outcome ? "YES" : "NO");
}
```

## Đổi thưởng token thắng

```typescript
// Người thắng nhận 1 USDC cho mỗi token thắng
const tx = await diamond.redeemMarketTokens(marketId);
await tx.wait();
```

## Giải quyết khẩn cấp (chỉ OPERATOR)

```typescript
// Có sẵn sau 7 ngày kể từ endTime nếu oracle chưa báo cáo
await diamond.emergencyResolve(marketId, true); // true = YES thắng
```

## Chế độ hoàn tiền (chỉ OPERATOR)

```typescript
// Bật hoàn tiền cho các thị trường có vấn đề
await diamond.enableRefundMode(marketId);

// Người dùng yêu cầu hoàn tiền theo tỷ lệ
await diamond.refund(marketId);
```

---

**Tiếp theo**: [Sự kiện](events.md) · [Lỗi](errors.md)
