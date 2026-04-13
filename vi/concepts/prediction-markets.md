---
description: Hiểu về thị trường dự đoán và cách giá phản ánh xác suất
---

# Thị trường dự đoán

## Thị trường dự đoán là gì?

Thị trường dự đoán là nơi người tham gia giao dịch dựa trên kết quả của các sự kiện tương lai. Giá token kết quả phản ánh đánh giá xác suất tập thể của đám đông.

## Cách giá phản ánh xác suất

Mỗi market tạo hai token:

- **Token YES**: Trả $1.00 nếu sự kiện xảy ra
- **Token NO**: Trả $1.00 nếu sự kiện KHÔNG xảy ra

Nếu YES giao dịch ở $0.70, thị trường ngụ ý xác suất **70%** rằng sự kiện sẽ xảy ra.

## Neo giá: YES + NO ≈ $1.00

Vì bất kỳ ai cũng có thể:

- **Tách** 1 USDC thành 1 YES + 1 NO
- **Gộp** 1 YES + 1 NO thành 1 USDC

Người chênh lệch giá duy trì:

```
Giá YES + Giá NO ≈ $1.00

Nếu YES + NO > $1.00 → tách + bán cả hai → lợi nhuận (đẩy giá xuống)
Nếu YES + NO < $1.00 → mua cả hai + gộp → lợi nhuận (đẩy giá lên)
```

Đây là bất biến cơ bản của mọi thị trường dự đoán.

## Lịch sử

| Nền tảng | Mô hình | Trạng thái |
| -------- | ----- | ------ |
| Polymarket | CLOB tập trung trên Polygon | Lớn nhất theo khối lượng, token ERC-1155 |
| Augur | AMM phi tập trung trên Ethereum | Tiên phong, áp dụng hạn chế |
| Kalshi | Sàn tập trung (CFTC quản lý) | Tiếp cận tài chính truyền thống |
| Omen (Gnosis) | AMM trên Gnosis Chain | Do cộng đồng dẫn dắt |
| **PrediX** | **CLOB+AMM kết hợp trên Uniswap v4** | **Token ERC-20, Diamond proxy** |

---

**Tiếp theo**: [Token kết quả](outcome-tokens.md) · [Tách & Gộp](split-and-merge.md) · [Cách hoạt động](../getting-started/how-it-works.md)
