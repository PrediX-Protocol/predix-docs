# Cấu trúc phí

PrediX có 4 loại phí. User thấy được tất cả trước khi xác nhận tx.

## 1. Phí AMM — dynamic theo thời gian

Phí swap qua Uniswap v4 pool, áp dụng bởi Hook:

| Thời gian tới endTime | Phí |
|---|---|
| > 7 ngày | 0.5% |
| 3–7 ngày | 1.0% |
| 1–3 ngày | 2.0% |
| < 24 giờ | 5.0% |

Phí trả bằng token in của swap. Phân bổ:
- Phần lớn → LP (reward cung cấp thanh khoản)
- Một phần → treasury protocol

**Tại sao dynamic**: Gần endTime, người có thông tin nội bộ ("toxic flow") trade nhiều hơn. LP cần spread rộng hơn để không lỗ. Fee cao → spread rộng → LP an tâm cung cấp liquidity tới phút cuối.

## 2. Phí CLOB

- **Maker** (đặt limit order): **0%** luôn.
- **Taker** (ăn market order qua sổ lệnh):
  - **0% từ launch đến Mainnet + 7 ngày** — để bootstrap thanh khoản.
  - **Sau đó dynamic 0–1%**, tuỳ thời gian endTime (giống AMM).

Maker free vì họ đang tạo thanh khoản. Taker trả vì họ lấy thanh khoản. Giống Polymarket, nhưng maker PrediX free permanent.

## 3. Phí tạo market

- Testnet: **$0**.
- Mainnet Phase 1: **$10–50 USDC / market** (tuỳ complexity).
- Mainnet Phase 3 (permissionless): Bond 10.000 PRX — trả lại khi market resolve thành công.

Phí tạo market → treasury. Chống spam + fund audit/resolve cho sự kiện.

## 4. Phí redemption

Phí khi redeem token thắng sau resolve:

- **Default**: 0% cho tới khi governance quyết định.
- **Cap tối đa**: **15%** (1500 bps) — hard enforce on-chain (invariant INV-4).
- **Snapshot tại thời điểm tạo market**: Admin tăng fee default không ảnh hưởng market đang tồn tại. Tránh retroactive fee rug.

Công thức redeem:
```
payout = winningAmount * (10000 - feeBps) / 10000
protocol_fee = winningAmount - payout
```

Ví dụ: redeem 100 YES đúng, fee 1%:
- `payout = 100 * 9900 / 10000 = 99 USDC`
- `protocol_fee = 1 USDC`

## Phí bạn **không** phải trả

- **Split USDC → YES+NO**: 0% phí protocol, chỉ gas.
- **Merge YES+NO → USDC**: 0%.
- **Cancel limit order**: 0% phí protocol.
- **Refund mode claim**: 0%.

## Gas trong tx

- EOA tự gọi → tự trả ETH gas (~0.0001 ETH ≈ $0.20 trên Unichain Sepolia).
- Smart account (passkey/MetaMask upgraded) → paymaster PrediX tài trợ. Free cho user.

Paymaster address testnet: `0x1637a7eB463b1b12906feF71eF23B76181340Cb7`.

## Phí phân bổ đi đâu

Dòng tiền phí protocol (AMM + CLOB taker + redemption + creation):

- **50%** → Staker PRX (real USDC yield — xem [Staking real yield](../kinh-te/staking-real-yield.md)).
- **30%** → Buyback PRX + burn (giảm supply — xem [Buyback burn](../kinh-te/buyback-burn.md)).
- **20%** → Treasury (audit, dev, LP subsidy qua gauge voting).

LP fee trên AMM đi riêng, thuộc về LP không chia về treasury.

## Ví dụ tổng kết tx

User mua 100 USDC YES trên market endTime còn 5 ngày:
- CLOB fill 40 USDC, 0% taker fee (trong 7 ngày post-mainnet) → nhận ~82 YES.
- AMM swap 60 USDC, 1% fee (3–7 ngày tier) → pay 0.6 USDC fee, nhận ~123 YES.
- **Tổng**: ~205 YES, trả 100 USDC, fee protocol = 0.6 USDC.

Nếu market resolve YES = true và fee redemption 1%:
- Redeem 205 YES → nhận `205 * 0.99 = 202.95 USDC`.
- **Lợi nhuận**: 102.95 USDC.

Nếu YES = false: mất toàn bộ 100 USDC.
