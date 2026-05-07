# Points — cách earn PRX

PrediX thưởng user bằng **Points** — đơn vị đo lường đóng góp. Points convert thành **PRX token** theo tỷ lệ pro-rata. Càng tham gia sớm, càng earn nhiều.

## S0 — Testnet (đang diễn ra)

**Pool 10M PRX** · 2-3 tháng trước mainnet · Cap 50K PRX/wallet.

| ID | Hoạt động | Points | Điều kiện | Tần suất |
|---|---|---|---|---|
| Q0.1 | Kết nối ví | 5 | 1 lần | Once |
| Q0.2 | Giao dịch testnet đầu tiên | 30 | Any market | Once |
| Q0.3 | 10 giao dịch testnet | 80 | Khác market/ngày | Once |
| Q0.4 | 50 giao dịch testnet | 150 | ≥5 markets, ≥7 ngày | Once |
| Q0.5 | LP ≥1,000 testUSDC | 60 | Hold LP ≥72h | Once |
| Q0.6 | Báo lỗi (verified) | 200-500 | Team verify + severity | Per bug |
| Q0.7 | X Follow + RT + Tag | 15 | Proof on Google Drive | Once |
| Q0.8 | Tạo nội dung | 100-300 | URL + community vote | Per content |
| Q0.9 | Giới thiệu (testnet) | 30/ref | Ref ≥5 trades, cap 50 refs | Ongoing |
| Q0.10 | Chuỗi 7 ngày | 25 | 1 trade/ngày | Weekly |

**Convert**: Points S0 → PRX qua Merkle proof tại TGE. Formula: `(Points bạn ÷ Tổng Points S0) × 10M PRX`, cap 50K PRX/wallet.

## S1 — Genesis (mainnet)

**Pool 56M PRX** (general trading) · M1-M6 · **Free period** — không phí giao dịch.

| ID | Hoạt động | Points | Điều kiện | Tần suất |
|---|---|---|---|---|
| Q1.1 | Kết nối ví | 10 | 1 lần | Once |
| Q1.2 | Giao dịch đầu tiên (real $) | 100 | Min $10 USDC | Once |
| Q1.3 | Mỗi $100 volume | 10 | Any market | Ongoing |
| Q1.4 | Giới thiệu — bạn kết nối ví | 50 | Cap 100 refs | Ongoing |
| Q1.5 | Giới thiệu — bạn trade ≥$10 | 100 | Funded wallet gate | Ongoing |
| Q1.6 | Cung cấp LP ≥$100 | 150 | Hold LP ≥7 ngày | Once |
| Q1.7 | Chuỗi 7 ngày | 50 | 1 trade/ngày min $5 | Weekly |
| Q1.8 | Chuỗi 14 ngày (bonus) | 120 | Extends 7-day streak | Bi-weekly |
| Q1.9 | Win streak 3 lần đúng | 30 | 3 correct predictions | Ongoing |
| Q1.10 | Đa dạng market | 20/market | ≥$50/market mới, cap 10 | Ongoing |

**Referral 2-gate**: giới thiệu bạn = **2 bước**. Bước 1: bạn kết nối ví (+50 pts). Bước 2: bạn trade ≥$10 (+100 pts). Cả 2 bước phải hoàn thành.

### FIFA World Cup boost (M5-M6)

Trong tháng 5-6 (FIFA WC), Points **boost 2.5-3×**:

| Hoạt động | Thường (M1-M4) | FIFA boost (M5-M6) |
|---|---|---|
| Giới thiệu bạn bè | 50 + 100 Points | **125 + 250 Points (2.5×)** |
| Bạn được giới thiệu | 100 Points | **300 Points (3×)** |
| Bạn GT trade $500+ trong FIFA | — | **+200 referrer / +100 referred** |
| Giới thiệu cấp 2 (indirect) | — | **+100 Points** |

### Convert Points → PRX

```
PRX nhận = (Points của bạn ÷ Tổng Points S1) × 56,000,000 PRX
```

- **Pro-rata**: càng nhiều người tham gia, mỗi Point chia ít PRX hơn.
- **3-Wave release**: PRX không phát hết 1 lần. Wave 1 (40%) tại TGE, Wave 2 (30%) tại TGE+30d, Wave 3 (30%) tại TGE+60d. Stake Wave 1 → boost Wave 2 (×1.1). Stake cả W1+W2 → boost Wave 3 (×1.2).
- **Không carry-over**: Points S0 / S1 tách biệt. Mỗi Season = pool riêng.
- **Cap**: 100K PRX/wallet (S1).

Chi tiết 3-Wave + season roadmap: [Points & seasons](../kinh-te/points-seasons.md).

## Anti-gaming

- **Cost gate**: S1 first trade min $10 USDC — chặn sybil accounts miễn phí
- **2-gate referral**: reward chỉ khi referee fund + trade ≥$10
- **Wash trade detection**: volume từ counterparty overlap bị flag
- **Duration lock**: LP phải hold ≥7 ngày (S1) — no flash deposit
- **Cap per wallet**: max Points/day + max 100 refs/wallet

## Theo dõi Points

App UI: **Profile → Points** tab.
- Current Points + quest progress
- Rank trong season
- Estimated PRX (pool / tổng Points)
- Referral tree + earnings

## KOL & Ambassador

Nếu bạn là content creator (≥1K followers): tham gia [KOL & Ambassador program](../kinh-te/kol-ambassador.md) — pool riêng 12M PRX, milestone-based.
