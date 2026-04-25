# Staking real yield

Lock PRX → nhận share 50% phí protocol dưới dạng **USDC thật**. Không phải emission.

## Cơ chế

1. Bạn deposit `N` PRX vào `StakingVault`.
2. Vault mint lại `N` stkPRX (non-transferable) cho bạn. Đây là claim share phí.
3. Mỗi week (epoch), protocol collect 50% phí USDC từ cả AMM + CLOB + redemption + creation fee → distribute vault pro-rata.
4. Bạn claim USDC bất cứ lúc nào. Hoặc auto-compound (convert về PRX rồi stake lại, nếu có option).
5. Unstake: cooldown 7 ngày (chống front-run khi event lớn).

## Tính yield

Công thức:
```
staker_share = 50% × total_fees_USDC_weekly
your_yield   = staker_share × (your_stake / total_staked)
APY_USDC     = (weekly_yield × 52) / your_stake_USD_value
```

Ví dụ tại steady state:
- Protocol volume $200M/tháng, fee mix ~0.3% → revenue $600k/tháng.
- Staker portion: $300k/tháng = $3.6M/năm USDC.
- Nếu total stake = 100M PRX @ giá $0.05 = $5M market cap staked.
- **APY_USDC = 3.6M / 5M = 72%**.

Với giá PRX cao hơn (hoặc stake thấp), APY giảm. Yield float theo volume thật.

## So sánh với staking model khác

| Model | Yield source | Sustainability |
|---|---|---|
| **Lido stETH** | ETH staking yield (~3%) | Cao |
| **Aave stkAAVE** | Emission stkAAVE + safety module | Trung bình (inflationary) |
| **GMX esGMX** | Fee USDC + ETH từ volume | Cao (real yield) |
| **PrediX stkPRX** | Fee USDC từ volume protocol | Cao (real yield, giống GMX) |

PrediX chọn GMX model vì:
- Không dilute supply PRX.
- Yield gắn với kết quả kinh doanh, không ép emission.
- User đo được: volume lên → yield lên.

## Fee tier boost (lock lâu hơn)

Bạn có thể lock PRX để nhận **boost yield + governance weight**:

| Lock duration | Yield boost | vePRX weight |
|---|---|---|
| No lock (stkPRX) | 1.0× | 0 (no vote) |
| 3 tháng | 1.1× | 0.25× |
| 6 tháng | 1.25× | 0.5× |
| 12 tháng | 1.5× | 1.0× |
| 24 tháng | 2.0× | 2.0× |
| 48 tháng (max) | 2.5× | 4.0× |

Boost đến từ treasury (20% phí). Lock càng lâu càng nhiều USDC yield + governance power.

Chi tiết governance: [vePRX & gauge](veprx-gauge.md).

## Fee discount cho staker

Stake ≥ ngưỡng → giảm phí giao dịch:

| Stake threshold | CLOB taker fee discount | AMM fee discount |
|---|---|---|
| 1,000 PRX | 10% | 0% |
| 10,000 PRX | 25% | 10% |
| 100,000 PRX | 50% | 25% |
| 1,000,000 PRX | 50% | 50% (cap) |

Active ngay lập tức khi stake, tính trong mỗi tx via merkle proof or on-chain check (TBD implementation).

## Rủi ro staker

- **Volume giảm**: yield giảm proportional. Không floor guarantee.
- **PRX price giảm**: USDC yield đổi sang USD value giảm (dù số USDC không đổi).
- **Smart contract risk**: Vault được audit cùng core protocol. Security posture: [Bảo mật](../giao-thuc/bao-mat.md).
- **Slashing**: Staker không bị slash ngoại trừ oracle dispute bond (Phase 2). Vault staking không có slash.

## Unstake flow

1. Click **Request unstake** trong UI.
2. Cooldown **7 ngày** bắt đầu. Trong thời gian này:
   - Bạn vẫn nhận yield (không rug bạn).
   - Không rút được PRX.
   - Không cancel được (tránh flip-flop).
3. Sau 7 ngày: **Claim** → PRX về ví.

Cooldown tránh user unstake + sell trước event lớn rồi restake ngay sau (giảm tính ổn định yield pool).

## Bao giờ staking khởi động

- **Không có** tại testnet (PRX chưa exist).
- **Launch cùng TGE** Q4 2026. Có thể có period pre-stake 1-2 tuần cho whitelist (pre-seed, seed, team) để bootstrap.
- Staking contract deploy + audit song song core, expect cùng mainnet.
