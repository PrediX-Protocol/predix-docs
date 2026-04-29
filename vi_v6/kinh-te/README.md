# PRX token & kinh tế

PRX là token quản trị + revenue share của PrediX. Hard cap **1 tỷ**, không mint thêm. ERC-20 trên Unichain.

![PRX allocation 1B total: Community 30% (300M, 6 seasons), Investors 20% (200M), Team 18% (180M, 12mo cliff), Treasury 17% (170M), Marketing 10% (100M), Liquidity 5% (50M, 100% TGE)](../_design/01-prx-allocation.svg)

## Token utility

- **Stake** → nhận share phí protocol bằng USDC (real yield, không emission)
- **Lock vePRX** → vote gauge, boost LP rewards 1.5-2.5×, protocol params governance
- **Market creation bond** → lock PRX để tạo permissionless market (Phase 2)
- **Oracle dispute** → lock PRX để challenge resolution sai

## TGE — conditions-based

PrediX dùng conditions-based TGE (không time-based):

| Metric | Threshold |
|---|---|
| Monthly volume | ≥ $500K × 3 tháng liên tiếp |
| Weekly Active Traders | ≥ 1,000 × 3 tháng |
| Active markets | ≥ 10 × 3 tháng |
| Smart contract audit | 0 critical, 0 high |

## Đọc tiếp

| Topic | Page |
|---|---|
| Allocation, vesting, TGE circulating | [Allocation & vesting](allocation-vesting.md) |
| Staking USDC yield, lock boost | [Staking real yield](staking-real-yield.md) |
| Lock → vePRX → vote → gauge → bribe | [vePRX & gauge](veprx-gauge.md) |
| Buyback-burn, treasury | [Buyback-burn](buyback-burn.md) |
| 6-season Points, referral program | [Points & seasons](points-seasons.md) |
| Badge, streak, daily challenge | [Rewards](rewards.md) |
