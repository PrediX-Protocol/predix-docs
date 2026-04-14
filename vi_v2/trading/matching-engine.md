# Matching Engine

CLOB của Exchange dùng thuật toán khớp ba giai đoạn với ba loại match khác nhau.

## Các loại match

### COMPLEMENTARY (type 0)

Hoán đổi trực tiếp giữa hai bên đối ứng.

```
BUY_YES ở $0.60 ↔ SELL_YES ở $0.55
→ Khớp ở $0.55 (giá maker). Buyer trả $0.55, nhận 1 YES. Hoàn lại: $0.05.
```

### MINT (type 1)

Hai lệnh mua kết hợp. Giao thức mint token mới từ USDC.

```
BUY_YES ở $0.72 + BUY_NO ở $0.30 = $1.02/cặp
→ Giao thức split $1.00 USDC → 1 YES + 1 NO
→ YES buyer trả $0.72, NO buyer trả $0.30
→ Dư $0.02 → doanh thu giao thức
```

Điều kiện: tổng giá ≥ $1.00

### MERGE (type 2)

Hai lệnh bán kết hợp. Giao thức merge token về USDC.

```
SELL_YES ở $0.40 + SELL_NO ở $0.55 = $0.95/cặp
→ Giao thức merge 1 YES + 1 NO → $1.00 USDC
→ YES seller nhận $0.40, NO seller nhận $0.55
→ Dư $0.05 → doanh thu giao thức
```

Điều kiện: tổng giá ≤ $1.00

## Cải thiện giá (price improvement)

Buyer luôn trả theo **giá của maker** (hoặc tốt hơn), không bao giờ theo giá limit mình đặt. Nếu bạn bid $0.70 và khớp với ask $0.50, bạn trả $0.50 và được hoàn $0.20.

## Độ ưu tiên khớp lệnh

1. Ưu tiên giá tốt nhất (bid cao nhất, ask thấp nhất)
2. FIFO ở cùng price level
3. Match COMPLEMENTARY được check trước MINT/MERGE

## Tiếp theo

- [Order Book](order-book.md) — đọc depth và spread
- [Lệnh giới hạn](limit-orders.md) — cách đặt lệnh
- [Exchange](../contracts/exchange.md) — chi tiết kỹ thuật
