---
description: Danh mục — thị trường có nhiều hơn hai kết quả
---

# Thị trường đa kết quả

## Danh mục

**Danh mục** nhóm nhiều thị trường YES/NO thành một sự kiện đa kết quả.

### Ví dụ: "Ai sẽ thắng cuộc bầu cử Tổng thống Mỹ 2028?"

```
Category: "US President 2028"
  Market 0: "Trump wins?"     → Token YES/NO
  Market 1: "DeSantis wins?"  → Token YES/NO
  Market 2: "Newsom wins?"    → Token YES/NO
  Market 3: "Harris wins?"    → Token YES/NO
  Market 4: "Other wins?"     → Token YES/NO
```

## GroupSplit & GroupMerge

### GroupSplit

Nạp `amount × N` USDC (trong đó N = số thị trường trong danh mục) để nhận `amount` YES + NO cho mỗi thị trường.

```typescript
// Danh mục có 5 thị trường. Nạp 500 USDC → 100 YES+NO mỗi thị trường
const amount = ethers.parseUnits("100", 6);
// Cần phê duyệt 100 × 5 = 500 USDC
await usdc.approve(DIAMOND_ADDRESS, ethers.parseUnits("500", 6));
await diamond.groupSplit(categoryId, amount);
```

### GroupMerge

Đốt `amount` YES + NO từ mọi thị trường để thu hồi `amount × N` USDC.

```typescript
await diamond.groupMerge(categoryId, amount);
// Đốt 100 YES + 100 NO từ mỗi thị trường trong 5 thị trường
// Trả lại 500 USDC
```

## Xác định kết quả

```typescript
// Chỉ 1 người thắng: thị trường tại chỉ số 2 thắng
await diamond.resolveCategory(categoryId, 2);

// Kết quả:
// Market 2 → YES thắng (người giữ YES đổi $1)
// Markets 0, 1, 3, 4 → NO thắng (người giữ NO đổi $1)
// Tất cả được xác định nguyên tử
```

> ⚠️ **Quan trọng**: `resolveCategory` được gọi bởi vai trò ADMIN và xác định kết quả cho TẤT CẢ thị trường trong danh mục đồng thời.

---

**Tiếp theo**: [Phí](fees.md) · [Tổng quan giao dịch](../trading/overview.md) · [Tạo thị trường (Nhà phát triển)](../developers/create-market.md)
