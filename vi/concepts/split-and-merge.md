---
description: Tách USDC thành token kết quả, hoặc gộp chúng lại
---

# Tách & Gộp

Tách và Gộp là các cơ chế nền tảng neo giá token kết quả vào giá trị thực.

## Tách

Nạp tài sản thế chấp USDC để nhận số lượng bằng nhau token YES và NO:

```
1 USDC → 1 YES + 1 NO
```

Tài sản thế chấp được khóa trong hợp đồng Diamond. Cung cấp của cả hai token tăng đều nhau.

## Gộp

Đốt số lượng bằng nhau token YES và NO để rút USDC:

```
1 YES + 1 NO → 1 USDC
```

Tài sản thế chấp được giải phóng từ Diamond. Cung cấp của cả hai token giảm đều nhau.

## Tại sao điều này quan trọng

Tách/Gộp tạo ra **neo giá** cho toàn bộ thị trường:

```
Nếu YES = $0.70 và NO = $0.40:
  YES + NO = $1.10 > $1.00
  → Người kinh doanh chênh lệch giá: tách $1 → bán YES $0.70 + bán NO $0.40 = $1.10
  → Lợi nhuận: $0.10
  → Áp lực bán đẩy giá trở về tổng ≈ $1.00
```

## Ví dụ mã

```typescript
import { ethers } from "ethers";

const diamond = new ethers.Contract(DIAMOND_ADDRESS, DIAMOND_ABI, signer);
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, signer);

const marketId = "0x...";
const amount = ethers.parseUnits("100", 6); // 100 USDC

// Bước 1: Phê duyệt USDC cho Diamond
await usdc.approve(DIAMOND_ADDRESS, amount);

// Bước 2: Tách — nhận 100 YES + 100 NO
await diamond.splitPosition(marketId, amount);

// Bước 3: Gộp — đốt 100 YES + 100 NO, nhận 100 USDC
await diamond.mergePositions(marketId, amount);
```

## Giới hạn an toàn

Thao tác tách chịu giới hạn an toàn:

| Giới hạn | Mô tả |
| -------- | ----- |
| **Giới hạn TVL** | USDC tối đa được khóa trong hệ thống |
| **Giới hạn mỗi giao dịch** | USDC tối đa cho mỗi thao tác tách |
| **Giới hạn mỗi thị trường** | USDC tối đa cho mỗi thị trường riêng lẻ |

> ⚠️ **Lưu ý**: Bạn phải giữ số lượng bằng nhau token YES và NO để gộp. Số lượng gộp không thể vượt quá số dư của bất kỳ token nào.

---

**Tiếp theo**: [Xác định kết quả](resolution.md) · [Thị trường đa kết quả](multi-outcome-markets.md) · [Tổng quan giao dịch](../trading/overview.md)
