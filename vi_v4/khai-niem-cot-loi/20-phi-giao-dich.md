# 20. Phí Giao Dịch & Redemption Fee Snapshot

Có 4 loại phí trong PrediX:

1. **AMM Dynamic Fee** (Hook)
2. **CLOB Maker/Taker Fee** (Exchange)
3. **MINT/MERGE Surplus** (Exchange dual-buy/sell matching)
4. **Redemption Fee** (tại redeem, fee snapshot at create)

## 1. AMM Dynamic Fees

Phí tăng dần khi market gần end time để bảo vệ LP khỏi adverse selection (informed traders):

| Thời gian còn lại | Phí |
|---|---|
| > 7 ngày | 0.5% |
| 3–7 ngày | 1.0% |
| 1–3 ngày | 2.0% |
| < 24 giờ | 5.0% |

Logic tính trong `Hook.beforeSwap` và return với `LPFeeLibrary.OVERRIDE_FEE_FLAG`. Chi tiết: [PrediXHookV2](../smart-contracts/31-hook-uniswap-v4.md#dynamic-fee).

## 2. CLOB Fees

Khởi điểm **0% maker/taker** (thu hút market makers). Roadmap: tăng taker fee dần lên 1% từ month+7.

Công thức (đối xứng):

```
fee = feeRate × min(price, 1 − price) × amount
```

**Lý do đối xứng**: risk của buy YES @ $0.7 và sell YES @ $0.3 tương đương nhau (đều là "taking 30% side"). Fee dựa trên side "cheaper" đảm bảo fairness.

Chi tiết: [PrediXExchange](../smart-contracts/29-exchange-clob.md#phí-clob).

## 3. MINT/MERGE Surplus

Khi CLOB match **MINT** (BUY_YES + BUY_NO, tổng > $1) hoặc **MERGE** (SELL_YES + SELL_NO, tổng < $1), phần thặng dư/thiếu hụt là doanh thu/chi phí protocol.

### Ví dụ MINT

- Person A đặt lệnh mua YES @ $0.72 (100 tokens)
- Person B đặt lệnh mua NO @ $0.30 (100 tokens)
- Tổng: $1.02 → Protocol đúc 100 cặp YES+NO mới, phát cho A + B
- Thặng dư `$0.02 × 100 = $2` vào fee recipient

### Ví dụ MERGE

- Person A đặt lệnh bán YES @ $0.50
- Person B đặt lệnh bán NO @ $0.45
- Tổng: $0.95 → Protocol merge 1 cặp, trả $0.95 chia về 2 người
- Thiếu hụt $0.05 → user "trả" chi phí exit

Chi tiết: [Matching Engine](../giao-dich/25-matching-engine.md).

## 4. Redemption Fee + Snapshot

{% hint style="warning" %}
**Fee redemption được "chụp" tại thời điểm tạo market**. Admin không thể raise fee retroactive — an toàn cho user đã mua token trước khi admin muốn tăng fee.
{% endhint %}

### Cơ chế

Khi user call `redeemMarketTokens(marketId)`, Diamond tính:

```
payout = amount × (1 − effectiveFeeBps / 10000)
fee    = amount × (effectiveFeeBps / 10000)
```

Trong đó `effectiveFeeBps` lấy từ:

- `perMarketRedemptionFeeBps` nếu `redemptionFeeOverridden = true`
- Else `snapshottedRedemptionFeeBps` (set tại `createMarket`)

**Hard cap**: `MAX_REDEMPTION_FEE_BPS = 1500 bps = 15%`.

### Tại sao snapshot?

Audit finding **FINAL-H04** phát hiện: nếu `defaultRedemptionFeeBps` là mutable live-value, admin có thể tăng fee ngay trước khi user redeem → ăn bớt profit. Fix = snapshot tại create, không ai có thể raise sau đó (chỉ có thể giảm qua `setPerMarketRedemptionFeeBps` — vẫn bị cap 1500).

Chi tiết: [Audit Reports](../security/01-audits.md).

## 5. Creation Fee (optional, admin-configurable)

Admin có thể set `marketCreationFeeBps` — phí trừ từ deposit đầu tiên của creator. Default 0 cho testnet.

## Phân phối doanh thu

Mọi fee (AMM, CLOB, MINT surplus, redemption) đều chuyển về `feeRecipient` address — thường là protocol treasury hoặc buyback mechanism.

Sau khi launch token, một phần doanh thu dự kiến dùng cho buyback + stake rewards — xem [Tokenomics](../tong-quan-du-an/07-tokenomics.md) và [Mô Hình Doanh Thu](../tong-quan-du-an/09-mo-hinh-doanh-thu.md).
