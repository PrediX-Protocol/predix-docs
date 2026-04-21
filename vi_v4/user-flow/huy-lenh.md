---
description: Cách hủy lệnh giới hạn đang mở
---

# Hủy lệnh

## Hủy lệnh giới hạn

Nếu bạn đã đặt lệnh giới hạn chưa được khớp:

1. Tìm lệnh đang mở trong phần **Lệnh**
2. Nhấn nút **"Cancel"** bên cạnh lệnh
3. Xác nhận trong ví
4. Tiền bị khóa được trả lại ngay lập tức

```
Trước khi hủy: $30 USDC bị khóa trong lệnh BUY
Sau khi hủy:  $30 USDC trở lại ví
```

## Cái gì được trả lại?

| Loại lệnh  | Được trả lại        |
| ---------- | ------------------- |
| BUY YES/NO | USDC (tiền đặt cọc) |
| SELL YES   | Token YES           |
| SELL NO    | Token NO            |

## Có thể hủy sau khi market hết hạn không?

**Có!** Bạn luôn có thể hủy lệnh, ngay cả sau khi market hết hạn. Tiền của bạn không bao giờ bị khóa vĩnh viễn.

> 💡 Nếu market hết hạn mà lệnh vẫn còn mở, bất kỳ ai cũng có thể hủy cho bạn — nhưng tiền hoàn luôn về **bạn** (chủ lệnh).

## Lệnh khớp một phần

Nếu lệnh được khớp một phần (vd: 40 trong 100 cổ phần đã khớp):

```
Ban đầu: BUY 100 YES @ $0.35 → khóa $35
Đã khớp: 40 cổ phần
Hủy phần còn lại: 60 cổ phần → hoàn $21 USDC
Bạn giữ: 40 cổ phần YES
```
