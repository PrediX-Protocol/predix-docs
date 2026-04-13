---
description: Hiểu giao diện giao dịch PrediX
---

# Hiểu giao diện

## Bố cục chính

Trang giao dịch có 4 khu vực chính:

```
┌──────────────────────┬─────────────────┐
│                      │  Vị thế của bạn │
│   Biểu đồ giá       │  YES: 65 cổ phần│
│                      ├─────────────────┤
│                      │  Bảng giao dịch │
│                      │  Mua / Bán      │
│                      │  Thị trường/    │
│                      │  Giới hạn       │
│                      │  [Số tiền]      │
│                      │  [Buy YES]      │
├──────────────────────┤                 │
│   Sổ lệnh           │  Tách / Gộp     │
│   Mua     Bán       │                 │
│   $0.35   $0.40      │  Thông tin market│
│   $0.34   $0.41      │                 │
└──────────────────────┴─────────────────┘
```

## Vị thế của bạn (trên phải)

Hiển thị những gì bạn đang nắm giữ trong market này:

```
Vị thế của bạn
  YES    65 cổ phần     ← bạn sở hữu 65 token YES
  NO     0 cổ phần      ← bạn không sở hữu token NO
```

## Bảng giao dịch (phải)

### Nút YES / NO
```
┌─────────┐ ┌─────────┐
│  YES    │ │   NO    │
│  38¢    │ │   62¢   │
└─────────┘ └─────────┘
```
- Tô xanh = đang chọn
- Giá hiển thị = giá thị trường hiện tại

### Chuyển đổi Mua / Bán
- **Mua**: chi USDC để nhận cổ phần
- **Bán**: bán cổ phần lấy USDC

### Chuyển đổi Thị trường / Giới hạn
- **Thị trường**: mua/bán ngay ở giá hiện tại
- **Giới hạn**: đặt giá riêng, chờ khớp

### Nhập số tiền
```
Số tiền:              Số dư: $17,927.54
┌──────────────────────────────────────┐
│ $ 50.00                         MAX │
└──────────────────────────────────────┘
  [$25]  [$50]  [$75]  [$100]
```
- Nhập số tiền hoặc nhấn nút preset
- **MAX** = toàn bộ số dư
- **Số dư** hiển thị để tham khảo

### Nút thực hiện
```
┌──────────────────────────────────────┐
│         Buy YES — $0.38              │
└──────────────────────────────────────┘
```
Hiển thị hành động và giá.

## Biểu đồ giá (trái)

Hiển thị lịch sử giá theo thời gian:
- Đường xanh = giá tăng
- Đường đỏ = giá giảm
- Di chuột để xem giá tại thời điểm cụ thể

## Sổ lệnh (dưới trái)

```
Giá     Số lượng     Tổng
$0.40    61.58      543.73    ← Ask (người bán)
$0.39    69.68      482.15
$0.38    70.34      412.47

         $0.38   Chênh lệch: $0.03      ← Giá giữa

$0.35    24.67      24.67     ← Bid (người mua)
$0.34    31.87      56.54
$0.33    31.29      87.83
```

- **Đỏ (trên)** = Ask — người muốn bán ở giá này
- **Xanh (dưới)** = Bid — người muốn mua ở giá này
- **Chênh lệch** = khoảng cách giữa giá bid tốt nhất và ask tốt nhất

## Bảng Tách / Gộp

```
┌──────────────────────────────┐
│  [Tách]          [Gộp]      │
│                              │
│  Tách USDC thành token       │
│  YES + NO bằng nhau          │
│                              │
│  ┌────────────────────┐      │
│  │ 100.00        USDC │      │
│  └────────────────────┘      │
│                              │
│  ┌────────────────────┐      │
│  │        Tách         │      │
│  └────────────────────┘      │
└──────────────────────────────┘
```

- **Tách**: $100 USDC → 100 YES + 100 NO token
- **Gộp**: 100 YES + 100 NO → $100 USDC
- Hữu ích cho chiến lược nâng cao

## Thông tin market

```
Thông tin market
  Danh mục:          crypto
  Tổng tài sản thế chấp: $9,936.67 USDC
  Trạng thái:        active
  Oracle:            0x699a8c...992cf3
```

- **Tổng tài sản thế chấp**: tổng USDC bị khóa trong market
- **Trạng thái**: active / ended / resolved
- **Oracle**: ai xác định kết quả

---

**Tiếp theo**: [Hủy lệnh](cancel-orders.md) · [Nhận thưởng](../resolution/claim-winnings.md)
