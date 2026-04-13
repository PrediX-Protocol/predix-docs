---
description: Cấu trúc phí cho AMM, CLOB và tạo thị trường
---

# Phí

## Phí động AMM

Phí swap trên pool AMM Uniswap v4 tăng lên khi thị trường tiến gần đến thời điểm hết hạn. Điều này bảo vệ nhà cung cấp thanh khoản khỏi các nhà giao dịch có thông tin ("độc hại") gần thời điểm thanh toán.

| Thời gian còn lại | Phí | Lý do |
| ------------------ | --- | ----- |
| > 7 ngày | 0.5% | Giao dịch bình thường, khuyến khích khối lượng |
| 3–7 ngày | 1.0% | Bắt đầu thận trọng |
| 1–3 ngày | 2.0% | Kết quả đang trở nên rõ ràng |
| < 24 giờ | 5.0% | Rủi ro cao cho LP, ngăn chặn dòng tiền độc hại |

## Phí CLOB

CLOB thu phí thông qua cơ chế thặng dư MINT và MERGE:

### Thặng dư MINT

Khi lệnh BUY_YES và BUY_NO khớp với tổng >= $1.00:

```
Fee = (takerPrice + makerPrice - $1.00) × amount

Ví dụ:
  BUY_YES @ $0.60 + BUY_NO @ $0.50 = $1.10
  Thặng dư = $0.10 mỗi token → kho bạc giao thức
```

### Thặng dư MERGE

Khi lệnh SELL_YES và SELL_NO khớp với tổng <= $1.00:

```
Fee = ($1.00 - takerPrice - makerPrice) × amount

Ví dụ:
  SELL_YES @ $0.40 + SELL_NO @ $0.50 = $0.90
  Thặng dư = $0.10 mỗi token → kho bạc giao thức
```

## Phí tạo thị trường

Có thể cấu hình cho từng thị trường. Được đặt thành **0** trong giai đoạn beta.

## Tóm tắt phí

| Nguồn | Phí | Người nhận |
| ----- | --- | ---------- |
| Swap AMM | 0.5%–5% (động) | Nhà cung cấp LP |
| Thặng dư CLOB MINT | Biến động | Kho bạc giao thức |
| Thặng dư CLOB MERGE | Biến động | Kho bạc giao thức |
| Tạo thị trường | Có thể cấu hình | Kho bạc giao thức |

---

**Tiếp theo**: [Tổng quan giao dịch](../trading/overview.md) · [Cung cấp thanh khoản](../liquidity/overview.md)
