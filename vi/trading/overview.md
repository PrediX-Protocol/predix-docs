---
description: Hai sàn giao dịch — CLOB và AMM — được hợp nhất bởi Smart Router
---

# Tổng quan giao dịch

PrediX cung cấp hai sàn giao dịch được hợp nhất bởi Smart Router:

```
User → PrediXRouter (Smart Router)
          ├── PrediXExchange (CLOB) — lệnh giới hạn, không trượt giá
          └── Uniswap v4 AMM — thanh khoản luôn sẵn sàng
```

## Lệnh thị trường vs Lệnh giới hạn

| Loại | Qua | Phù hợp cho |
| ---- | --- | ------------ |
| **Lệnh thị trường** | Router (`buyYes`, `sellYes`, `buyNo`, `sellNo`) | Thực hiện ngay, giá tốt nhất |
| **Lệnh giới hạn** | Exchange (`placeOrder`) | Giá cụ thể, nhà giao dịch kiên nhẫn |

## Quy trình giao dịch

1. **Phê duyệt** USDC (hoặc token) cho hợp đồng
2. **Truy vấn** đầu ra dự kiến bằng `quoteBuyYes` / `quoteSellYes` v.v.
3. **Thực hiện** giao dịch với bảo vệ trượt giá (tham số `minOut`)
4. **Nhận** token kết quả (hoặc USDC)

> ⚠️ **Luôn đặt thời hạn** (thường là 5 phút từ bây giờ) và **minOut** để bảo vệ trượt giá.

---

**Tiếp theo**: [Lệnh thị trường](market-orders.md) · [Lệnh giới hạn](limit-orders.md) · [Định tuyến thông minh](smart-routing.md)
