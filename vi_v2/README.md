# PrediX Protocol

**Thị trường dự đoán phi tập trung trên Uniswap v4**

PrediX là giao thức prediction market chạy hoàn toàn on-chain, triển khai trên Unichain (L2 của Uniswap). Bạn giao dịch kết quả nhị phân bằng token ERC-20 thật, với thanh khoản kết hợp (hybrid) và cơ chế quyết toán không cần tin cậy bên thứ ba.

- **Làm gì:** Mua/bán token YES/NO với giá từ $0.01 đến $0.99 (tính bằng USDC). Giá token phản ánh xác suất thị trường đánh giá sự kiện sẽ xảy ra.
- **Vì sao đặc biệt:** Là prediction market đầu tiên dùng token ERC-20 có thể kết hợp (composable) với DeFi, thanh khoản lai CLOB + AMM, và phí AMM động theo thời gian — tất cả nhờ Uniswap v4 Hooks.
- **Cách dùng:** Smart Router tự động chia lệnh giữa sổ lệnh on-chain và pool AMM để cho giá tốt nhất. Oracle quyết định kết quả. Người thắng đổi mỗi token lấy $1 USDC.

## Liên kết nhanh

| Tôi muốn… | Đi tới |
|------------|-------|
| Giao dịch | [Tổng quan giao dịch](trading/overview.md) |
| Build trên PrediX | [Developer Quickstart](developers/quickstart.md) |
| Hiểu giao thức | [PrediX là gì?](getting-started/what-is-predix.md) |
| Xem smart contracts | [Tổng quan contracts](contracts/overview.md) |

---

**Mạng:** Unichain Sepolia Testnet (Chain ID: 1301)
**Trạng thái:** Đã live trên testnet — 264 tests pass, OWASP 10/10
