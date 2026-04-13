---
description: Thuật toán khớp lệnh ba giai đoạn — COMPLEMENTARY, MINT và MERGE
---

# Công cụ khớp lệnh

PrediXExchange sử dụng bitmap 99 tick để quét lệnh hiệu quả gas trên ba loại khớp lệnh.

## Các loại khớp lệnh

### COMPLEMENTARY — Hoán đổi trực tiếp

Hai lệnh đối lập cho cùng một token:

```
BUY_YES @ $0.60  ↔  SELL_YES @ $0.50
→ Khớp tại giá maker ($0.50)
→ Người mua nhận YES, người bán nhận USDC
→ Người mua nhận cải thiện giá $0.10 (hoàn tiền)
```

### MINT — Cả hai bên mua (tổng >= $1.00)

```
BUY_YES @ $0.60  +  BUY_NO @ $0.50  =  $1.10 ≥ $1.00
→ Tách USDC → YES + NO
→ YES giao cho người mua YES, NO giao cho người mua NO
→ Thặng dư $0.10 → phí giao thức
```

### MERGE — Cả hai bên bán (tổng <= $1.00)

```
SELL_YES @ $0.40  +  SELL_NO @ $0.50  =  $0.90 ≤ $1.00
→ Gộp YES + NO → USDC
→ USDC phân chia giữa người bán theo giá
→ Thặng dư $0.10 → phí giao thức
```

## Enum MatchType

```solidity
enum MatchType {
    COMPLEMENTARY, // 0
    MINT,          // 1
    MERGE          // 2
}
```

## Ưu tiên khớp lệnh

Khi một lệnh mới được đặt, công cụ thử khớp theo thứ tự này:

1. **COMPLEMENTARY** — kiểm tra lệnh đối tác trực tiếp
2. **MINT** — kiểm tra xem lệnh mua phía đối diện có thể ghép đôi không
3. **MERGE** — kiểm tra xem lệnh bán phía đối diện có thể ghép đôi không
4. **Còn lại** — số lượng chưa khớp trở thành lệnh chờ trên sổ lệnh

---

**Tiếp theo**: [Sổ lệnh](order-book.md) · [Định giá NO ảo](virtual-no-pricing.md)
