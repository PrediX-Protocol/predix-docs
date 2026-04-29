# Trade lần đầu

Mua YES hoặc NO trên một market. ~30 giây từ click đến confirm.

## Bước

1. Vào [Markets](https://app.predix.app/markets), browse hoặc search market quan tâm.
2. Click vào card → trang detail.
3. Panel bên phải: chọn tab **Buy**.
4. Chọn side **YES** (sự kiện sẽ xảy ra) hoặc **NO** (không xảy ra).
5. Nhập amount USDC chi (ví dụ: 100).
6. Preview hiển thị:
   - Amount YES / NO nhận được
   - Average price
   - Slippage estimate
   - Tỷ lệ split CLOB / AMM
7. Click **Buy** → ví request confirm (Touch ID nếu passkey, popup MetaMask nếu EOA).
8. Tx confirm trong ~2 giây trên Unichain. Vị thế xuất hiện ở [Portfolio](../huong-dan/portfolio.md).

## Đang xảy ra gì bên dưới

![First trade: connect wallet → bridge USDC → chọn market → buy YES/NO → nhận outcome tokens](../_design/11-first-trade.svg)

Tất cả 1 tx atomic. Slippage > tolerance → revert, tiền không mất.

## Loại lệnh

| Loại | Khi nào dùng |
|---|---|
| **Market** (instant) | Vào ngay theo giá hiện tại |
| **Limit** (CLOB) | Đặt giá chờ khớp |
| **Split** | Mint cặp YES+NO từ USDC để market-make |
| **Merge** | Burn cặp YES+NO lấy USDC |

Chi tiết: [Market order](../huong-dan/giao-dich-market.md), [Limit order](../huong-dan/dat-lenh-limit.md).

## Ví dụ thực tế

Market: *"BTC vượt $100k trước 2027-01-01?"*. Giá YES hiện = $0.48.

Bạn chi 100 USDC mua YES:

| Path | Amount in | Avg price | YES out |
|---|---|---|---|
| CLOB (limit orders sẵn) | 40 USDC | $0.480 | 83.3 YES |
| AMM swap | 60 USDC | $0.485 | 122.7 YES |
| **Tổng** | **100 USDC** | **$0.483** | **~205 YES** |

Nếu BTC vượt $100k trước deadline:
- Market resolve YES = true.
- Bạn redeem 205 YES → nhận USDC. Lợi nhuận > 100 USDC.

Nếu không xảy ra:
- YES tokens = $0. Lỗ 100 USDC.

## Sell vị thế

Cùng panel, tab **Sell**:

1. Chọn YES hoặc NO bạn đang giữ.
2. Nhập amount muốn sell.
3. Preview USDC nhận về.
4. Confirm.

Router tìm path tốt nhất ngược lại — drain bid orders CLOB trước, swap AMM phần còn lại.

## Hold tới resolve

Không sell — giữ token tới khi market resolve, sau đó redeem 1:1 USDC nếu thắng. Chi tiết: [Redeem & refund](../huong-dan/redeem-va-claim.md).

## Lỗi thường gặp lần đầu

- **"Insufficient USDC balance"** — Bridge USDC sang Unichain trước. Xem [Bridge](bridge.md).
- **"Slippage exceeded"** — Giá chạy quá tolerance trong lúc tx pending. Tăng slippage (default 0.5% → 1%) hoặc retry.
- **"Wallet not connected"** — Click Sign in / Connect wallet ở header.
- **"Market paused"** — Hiếm — admin pause market vì lý do bảo mật. Xem thông báo trong UI.
