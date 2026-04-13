---
description: Tính năng nâng cao — tạo và kết hợp lại token
---

# Tách & Gộp (Nâng cao)

> Đây là tính năng nâng cao. Hầu hết trader không cần dùng.

## Tách (Split) là gì?

**Tách** chuyển đổi USDC thành lượng bằng nhau token YES và NO:

```
Tách $100 USDC → 100 token YES + 100 token NO
```

## Gộp (Merge) là gì?

**Gộp** là ngược lại — kết hợp YES và NO trở lại USDC:

```
100 token YES + 100 token NO → $100 USDC
```

## Tại sao lại dùng?

### Lý do 1: Chênh lệch giá

Nếu YES = $0.40 và NO = $0.45 (tổng $0.85):

```
Tách $1 → 1 YES + 1 NO (chi phí: $1)
Bán YES $0.40 + Bán NO $0.45 = $0.85
Lỗ: $0.15 ← không có lãi theo cách này

NHƯNG nếu YES = $0.55 và NO = $0.55 (tổng $1.10):
Mua 1 YES ($0.55) + Mua 1 NO ($0.55) = $1.10
Gộp → $1.00
Lỗ: $0.10 ← cũng không có lãi

Chênh lệch giá chỉ hoạt động khi tổng ≠ $1.00
```

### Lý do 2: Lấy cả hai token

Nếu bạn muốn làm market maker và bán cả hai phía:

```
Tách $500 → 500 YES + 500 NO
Đặt lệnh SELL YES ở $0.55
Đặt lệnh SELL NO ở $0.55
Kiếm từ cả hai phía
```

### Lý do 3: Phòng hộ

Nếu bạn đã giữ YES và muốn giảm rủi ro:

```
Bạn giữ 100 YES (mua ở $0.40)
Tách $50 → 50 YES + 50 NO
Bây giờ: 150 YES + 50 NO
Nếu NO thắng: bạn nhận $50 từ NO (phòng hộ một phần)
```

## Cách tách

1. Đi đến trang market
2. Tìm phần **"Split / Merge"**
3. Nhấn tab **"Split"**
4. Nhập số tiền USDC
5. Nhấn **"Split"**
6. Xác nhận trong ví

## Cách gộp

1. Phần **"Split / Merge"**
2. Nhấn tab **"Merge"**
3. Nhập số lượng (phải có YES và NO bằng nhau)
4. Nhấn **"Merge"**
5. USDC trả về ví

> ⚠️ Bạn cần lượng YES và NO **bằng nhau** để gộp. Nếu bạn có 100 YES nhưng chỉ 50 NO, bạn chỉ có thể gộp 50.

---

**Tiếp theo**: [Mua & Bán](../trading/buy-sell.md) · [Cung cấp thanh khoản](../../liquidity/overview.md)
