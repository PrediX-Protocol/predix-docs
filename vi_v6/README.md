# PrediX

Prediction market on-chain trên Unichain. Mỗi sự kiện có hai outcome token YES / NO; token đúng redeem 1:1 USDC khi market resolve.

**Ví dụ**: Market *"Bitcoin vượt $100k trước 2027?"* tạo ra 2 token YES + NO. User mua bên mình tin sẽ thắng.

| Khi market resolve | YES holder | NO holder |
|---|---|---|
| Sự kiện **xảy ra** | Nhận `$1 USDC / token` | `$0` (mất hết) |
| Sự kiện **không xảy ra** | `$0` (mất hết) | Nhận `$1 USDC / token` |

---

## Đọc gì trước

| Bạn là… | Bắt đầu từ |
|---|---|
| Trader muốn vào ngay | [Bắt đầu](bat-dau/README.md) |
| Muốn hiểu sản phẩm trước | [Khái niệm](khai-niem/README.md) |
| Cần tutorial từng bước | [Hướng dẫn](huong-dan/README.md) |
| Quan tâm cộng đồng + reward | [Cộng đồng](cong-dong/README.md) |
| Stake PRX + governance | [Kinh tế](kinh-te/README.md) |
| Builder tích hợp | [Developers](developers/README.md) |

Đọc sâu giao thức + bảo mật: [Giao thức](giao-thuc/README.md).

---

## Điểm khác biệt

- **Outcome token là ERC-20** — composable với DeFi (LP, collateral, vault, lending). Không phải ERC-1155 như Polymarket.
- **Hybrid CLOB + AMM** — Router tự động split lệnh giữa on-chain order book và Uniswap v4 pool, lấy giá tốt nhất trong cùng tx.
- **Real yield** — 50% phí protocol chảy về staker dưới dạng USDC thật, 30% buyback-burn PRX. Không emission.
- **Non-custodial** — Router stateless, bất biến `balanceOf(router) == 0` enforce on-chain. Diamond + Hook upgrade qua 48h timelock, không emergency bypass.
- **Account abstraction** — đăng nhập bằng passkey hoặc wallet. Gas sponsor qua paymaster.

---

## Liên kết

- App: [app.predix.app](https://app.predix.app)
- Explorer: [uniscan.xyz](https://uniscan.xyz)
- Contract addresses: [giao-thuc/addresses.md](giao-thuc/addresses.md)
- Bug bounty + audit: [giao-thuc/bao-mat.md](giao-thuc/bao-mat.md)
- Discord, Twitter, GitHub: [tai-nguyen/links.md](tai-nguyen/links.md)
