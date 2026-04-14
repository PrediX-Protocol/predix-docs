# FAQ

Giải đáp nhanh các câu hỏi thường gặp. Chi tiết hơn, hãy theo link.

## Tổng quan

**PrediX là gì?**
→ Xem [PrediX là gì?](../getting-started/what-is-predix.md)

**PrediX chạy trên chain nào?**
Unichain Sepolia Testnet (Chain ID: 1301). Xem [Tổng quan contracts](../contracts/overview.md).

**Tôi bắt đầu giao dịch thế nào?**
→ Xem [Khởi động nhanh](../getting-started/quick-start.md)

## Giao dịch

**Tôi có thể lỗ nhiều hơn số vốn bỏ ra không?**
Không. Lỗ tối đa là số tiền bạn đã bỏ ra mua token. Token YES/NO không bao giờ có giá trị âm. Nếu bạn mua 100 YES ở $0.60, lỗ tối đa là $60 (nếu market resolve NO).

**Giá được xác định thế nào?**
→ Xem [Thị trường dự đoán](../concepts/prediction-markets.md)

**Phí là bao nhiêu?**
→ Xem [Phí](../concepts/fees.md)

**Split/merge là gì?**
→ Xem [Split & Merge](../concepts/split-and-merge.md)

## Kỹ thuật

**Làm sao lấy USDC test?**
Dùng [Unichain Sepolia Faucet](https://faucet.unichain.org) hoặc mint trực tiếp từ contract test USDC. Xem [Khởi động nhanh](../getting-started/quick-start.md).

**PrediX đã được audit chưa?**
Rồi — 264 tests, 3 lần audit AI độc lập, phân tích Slither, fuzz test. Xem [Bảo mật](../contracts/security.md).

**Điều gì xảy ra nếu oracle lỗi?**
Emergency resolution kích hoạt sau 7 ngày kể từ endTime. Nếu vẫn lỗi, refund mode sẽ trả USDC theo tỷ lệ. Xem [Phân xử](../concepts/resolution.md).

## Tiếp theo

- [Bảng thuật ngữ](glossary.md) — tham chiếu thuật ngữ
- [Cộng đồng](community.md) — nhận hỗ trợ
