# Bắt đầu

Trade lần đầu trên PrediX trong dưới 2 phút.

## Overview

| 🔐 **Setup** (~10s) | 💰 **Funding** (~30s) | 📈 **Trade** (~20s) |
|---|---|---|
| Mở app | Bridge USDC từ chain khác | Browse market |
| Sign in (passkey / MetaMask) | Hoặc nạp trực tiếp từ CEX | Buy YES hoặc NO |
| | | Theo dõi P\&L realtime |

## 3 bước

1. **[Kết nối ví](ket-noi-vi.md)** — Passkey (sinh trắc học, UX web2) hoặc crypto wallet (MetaMask / Rainbow / WalletConnect). User đủ điều kiện chương trình có thể nhận gas sponsor qua paymaster.
2. **[Bridge USDC](bridge.md)** — Nếu USDC của bạn đang ở chain khác, bridge sang Unichain trước.
3. **[Trade lần đầu](giao-dich-dau-tien.md)** — Buy YES hoặc NO trong 30 giây.

**Earn PRX ngay**: Mọi action trên PrediX earn [Points](points.md) — convert thành PRX token tại TGE. FIFA WC boost 2.5-3× trong M5-M6.

Theo dõi vị thế realtime ở [Portfolio](../huong-dan/portfolio.md).

## Hỏi nhanh

- [Passkey vs MetaMask, chọn cái nào?](faq.md#passkey-vs-metamask)
- [Tại sao YES + NO luôn ≈ $1?](faq.md#yes-no-1-dollar)
- [Có phí gas không?](faq.md#gas-co-mat-tien)
- [USDC từ Binance, Coinbase chuyển sang được không?](faq.md#nap-tu-cex)
- [Mất thiết bị có passkey thì sao?](faq.md#mat-passkey)

[Xem toàn bộ FAQ →](faq.md)

## Network info

| | Testnet (live now) | Mainnet (sắp ra) |
|---|---|---|
| **Network** | Unichain Sepolia | Unichain |
| **Chain ID** | `1301` | `130` |
| **RPC public** | `https://sepolia.unichain.org` | `https://mainnet.unichain.org` |
| **Explorer** | [sepolia.uniscan.xyz](https://sepolia.uniscan.xyz) | [uniscan.xyz](https://uniscan.xyz) |
| **USDC** | `0x2D56777Af1B52034068Af6864741a161dEE613Ac` (test-USDC) | TBA |
| **Faucet** | Có — 0.0005 ETH + 10k USDC / 24h / ví | N/A |
| **Status** | ✅ Beta live cho user + dev | 🟡 TBA — sau external audit |

> **Hiện tại bạn đang dùng testnet beta**. Mọi giao dịch trên testnet không có giá trị thật — số dư + history sẽ reset trước khi mainnet launch. Hoạt động testnet được track qua points campaign → convert PRX tại TGE.

> **Tip**: App tự động add network vào ví khi bạn kết nối lần đầu. Không cần thêm thủ công.

Chi tiết testnet (faucet, API endpoint, develop flow): [Testnet info](../developers/testnet.md).
