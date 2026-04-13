---
description: Câu hỏi thường gặp về PrediX Protocol
---

# FAQ

## PrediX là gì?

PrediX Protocol là nền tảng thị trường dự đoán phi tập trung được xây dựng trên Uniswap v4 (Unichain L2). Nó kết hợp CLOB on-chain và AMM để cung cấp trải nghiệm giao dịch tốt nhất cho thị trường dự đoán.

## Làm sao để bắt đầu giao dịch?

1. Kết nối ví với Unichain Sepolia
2. Nhận USDC thử nghiệm từ faucet
3. Phê duyệt USDC cho contract Router
4. Gọi `buyYes` hoặc `buyNo` qua Router

Xem hướng dẫn [Bắt đầu nhanh](../getting-started/quick-start.md) để biết chi tiết.

## Điều gì xảy ra khi market được giải quyết?

Oracle báo cáo kết quả, sau đó bất kỳ ai cũng có thể gọi `resolveMarket`. Người giữ token thắng nhận $1.00 mỗi token. Token thua trở nên vô giá trị.

## Tôi có thể mất nhiều hơn số đã đầu tư không?

**Không.** Thua lỗ tối đa là số tiền bạn trả cho token kết quả. Không có đòn bẩy, ký quỹ hay thanh lý.

## Giá được xác định như thế nào?

Giá được đặt bởi cung và cầu qua hai nguồn: pool AMM và sổ lệnh CLOB. Smart Router tổng hợp cả hai nguồn. Giá tự nhiên phản ánh ước tính xác suất tập thể của thị trường.

## Split/Merge là gì?

- **Split**: Gửi 1 USDC → nhận 1 YES + 1 NO token
- **Merge**: Đốt 1 YES + 1 NO → nhận 1 USDC

Cơ chế này neo giá token sao cho YES + NO ≈ $1.00.

## Phí là bao nhiêu?

Phí AMM từ 0.5% (hơn 7 ngày trước hết hạn) đến 5% (dưới 24 giờ). Phí CLOB đến từ phần dư MINT/MERGE. Xem [Phí](../concepts/fees.md) để biết chi tiết.

## PrediX đã được kiểm toán chưa?

Có. 264 test, 14 fuzz test (mỗi cái 10K lần), 31 test E2E on-chain, 3 kiểm toán độc lập với 22 lỗi phát hiện và sửa, phạm vi OWASP SC Top 10 2026 10/10. Xem [Báo cáo kiểm toán](../security/audit-reports.md).

## PrediX trên chain nào?

Unichain Sepolia (testnet). Triển khai mainnet trên Unichain dự kiến Q3 2026.

## Làm sao nhận USDC thử nghiệm?

Truy cập faucet testnet PrediX hoặc mint USDC thử nghiệm trực tiếp tại địa chỉ `0x12fd156C8b5F2901BA2781d97db84AaC56b2b911` trên Unichain Sepolia.

---

**Tiếp theo**: [Thuật ngữ](glossary.md) · [Bắt đầu nhanh](../getting-started/quick-start.md)
