# Staking real yield

Lock PRX → nhận share **20-35% phí protocol** (adaptive theo growth phase) dạng **USDC thật**. Không emission.

> **Adaptive split**: staker share thay đổi theo phase — Bootstrap 20% · Scale 30% · Mature 35% · Dominance 30%. Detail: [Buyback-burn §Adaptive split](buyback-burn.md#adaptive-4-phase-split).

## Cơ chế

![Staking flow](../_design/07-staking-flow.svg)

1. Deposit N PRX vào `StakingVault`.
2. Vault mint N **stkPRX** (non-transferable) cho bạn — claim share fee.
3. Mỗi week (epoch), protocol collect **adaptive % fee USDC** (20-35% per growth phase) từ AMM + CLOB + redemption + creation → distribute vault pro-rata.
4. Claim USDC bất cứ lúc nào, hoặc auto-compound (convert PRX rồi re-stake).
5. Unstake: cooldown 7 ngày (chống front-run khi event lớn).

## Tính yield

```
staker_share = phase_pct × total_fees_USDC_weekly  (phase_pct = 20-35%)
your_yield   = staker_share × (your_stake / total_staked)
APY_USDC     = (weekly_yield × 52) / your_stake_USD_value
```

Yield float theo volume thật. Volume tăng → yield tăng. Volume giảm → yield giảm.

Yield float theo volume thật. Volume tăng → yield tăng. Volume giảm → yield giảm.

## Lock boost

Lock PRX để nhận **boost yield + governance weight**:

```mermaid
flowchart LR
    Stake[Stake không lock<br/>stkPRX] -->|1.0× yield| Y0[USDC yield base]
    Lock3[Lock 3m] -->|1.1×| Y1[USDC yield × 1.1]
    Lock6[Lock 6m] -->|1.25×| Y2[USDC yield × 1.25]
    Lock12[Lock 12m] -->|1.5×| Y3[USDC yield × 1.5]
    Lock24[Lock 24m] -->|2.0×| Y4[USDC yield × 2.0]
    Lock48[Lock 48m max] -->|2.5×| Y5[USDC yield × 2.5]

    classDef low fill:#475569,stroke:#334155,color:#fff,stroke-width:1.5px
    classDef high fill:#16a34a,stroke:#15803d,color:#fff,stroke-width:2px
    class Stake,Y0 low
    class Lock48,Y5 high
```

| Lock | Yield boost | vePRX weight |
|---|---|---|
| No lock (stkPRX) | 1.0× | 0 |
| 3 tháng | 1.1× | 0.25× |
| 6 tháng | 1.25× | 0.5× |
| 12 tháng | 1.5× | 1.0× |
| 24 tháng | 2.0× | 2.0× |
| 48 tháng (max) | 2.5× | 4.0× |

Boost từ treasury (20% phí). Lock càng lâu = yield USDC + governance power càng cao.

Chi tiết governance: [vePRX & gauge](veprx-gauge.md).

## Fee discount cho staker

Stake ≥ ngưỡng → giảm fee giao dịch:

| Stake threshold | CLOB taker discount | AMM discount |
|---|---|---|
| 1,000 PRX | 10% | 0% |
| 10,000 PRX | 25% | 10% |
| 100,000 PRX | 50% | 25% |
| 1,000,000 PRX | 50% (cap) | 50% (cap) |

Discount active ngay khi stake, apply tự động trong mỗi tx.

## Rủi ro staker

| Risk | Mitigation |
|---|---|
| **Volume giảm** → yield giảm | Diversify nhiều income stream (LP, content) |
| **PRX price giảm** → USD value yield giảm | Stake để long-term, không trade short-term |
| **Smart contract risk** | Vault audit cùng core protocol. Bug bounty active |
| **Slashing** | Vault staking **không** bị slash (chỉ oracle dispute bond Phase 2 mới có slash) |

## Unstake flow

![Unstake cooldown](../_design/24-unstake-cooldown.svg)
