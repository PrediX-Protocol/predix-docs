# PrediX V2

Thị trường dự đoán phi tập trung trên Unichain. Mỗi sự kiện có hai token YES/NO; token đúng được redeem 1:1 USDC khi sự kiện được giải quyết.

**Trạng thái**: Testnet Unichain Sepolia (chain `1301`). Mainnet dự kiến Q2 2026 sau khi audit bên ngoài hoàn tất.

---

## Đọc gì trước

| Bạn là… | Bắt đầu từ |
|---|---|
| Trader muốn giao dịch ngay | [Bắt đầu](bat-dau/README.md) |
| Muốn hiểu sản phẩm trước | [Khái niệm](khai-niem/README.md) |
| Cần tutorial theo tác vụ | [Hướng dẫn](huong-dan/README.md) |
| Quan tâm PRX token & staking | [Kinh tế](kinh-te/README.md) |
| Nhà phát triển tích hợp | [Developers](developers/README.md) |

Tham khảo sâu về giao thức: [Kiến trúc & bảo mật](giao-thuc/README.md).

---

## Điểm khác biệt

- **Token kết quả ERC-20** — composable với DeFi (LP, collateral), không phải ERC-1155 như Polymarket.
- **Sổ lệnh + AMM lai** — Router tự động split lệnh giữa on-chain CLOB và Uniswap v4 pool để tối ưu giá.
- **Thanh toán thật** — 50% phí protocol chảy về staker dưới dạng USDC thật, 30% buyback-burn PRX.
- **Không lưu ký** — Router stateless, số dư router sau mỗi giao dịch phải bằng 0 (bất biến kiểm tra on-chain).

---

## Liên kết

- App testnet: [app.predix.app](https://app.predix.app) (đang beta)
- Explorer: [sepolia.uniscan.xyz](https://sepolia.uniscan.xyz)
- Contract addresses: [giao-thuc/addresses.md](giao-thuc/addresses.md)
- Bug bounty & audit: [giao-thuc/bao-mat.md](giao-thuc/bao-mat.md)
