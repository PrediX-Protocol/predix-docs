# Thị trường dự đoán

Prediction market là nơi mọi người giao dịch dựa trên kết quả của các sự kiện tương lai. Giá token ở đây phản ánh ước lượng xác suất của đám đông theo thời gian thực.

## Giá phản ánh xác suất như thế nào

Mỗi market có hai loại token:

- **YES** — trả $1.00 nếu sự kiện xảy ra
- **NO** — trả $1.00 nếu sự kiện không xảy ra

Nếu YES đang được giao dịch ở $0.72, thị trường ngầm định xác suất sự kiện xảy ra là **72%**.

## Bất biến $1

YES + NO ≈ $1.00 luôn đúng. Nếu quan hệ này bị phá vỡ, arbitrager sẽ kiếm lời:

- **YES + NO > $1:** Mua cả hai, merge thành USDC, chốt lời phi rủi ro
- **YES + NO < $1:** Split USDC thành cặp, bán cả hai thu về nhiều hơn số đã bỏ ra

Cơ chế này giữ cho giá trên PrediX luôn hiệu quả. Xem [Split & Merge](split-and-merge.md) để rõ hơn.

## PrediX khác biệt ở đâu

| Nền tảng | Loại | Tính composable | Thanh khoản |
|----------|------|---------------|-----------|
| Polymarket | Chỉ CLOB | ERC-1155 (không composable) | Sâu nhưng chỉ một venue |
| Augur/Omen | Chỉ AMM | Hạn chế | Mỏng, full-range |
| **PrediX** | **CLOB + AMM** | **ERC-20 (toàn DeFi)** | **Hybrid, concentrated** |

Token YES/NO trên PrediX là ERC-20 chuẩn — có thể LP trên Uniswap, làm tài sản thế chấp, hoặc tích hợp vào bất kỳ giao thức DeFi nào. Không prediction market lớn nào khác có tính năng này.

## Tiếp theo

- [Token kết quả](outcome-tokens.md) — cơ chế token ERC-20
- [Split & Merge](split-and-merge.md) — cơ chế neo giá
- [Phí](fees.md) — biểu phí động
