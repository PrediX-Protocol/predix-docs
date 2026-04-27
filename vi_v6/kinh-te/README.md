# PRX token & kinh tế

PRX là token quản trị + revenue share của PrediX. Hard cap **1 tỷ**, không mint thêm. ERC-20 trên Unichain.

```mermaid
pie title PRX Allocation (1B total)
    "Community & Ecosystem (30%)" : 30
    "Investors (20%)" : 20
    "Team & Advisors (18%)" : 18
    "Treasury / DAO (17%)" : 17
    "Marketing & Partners (10%)" : 10
    "Liquidity DEX/CEX (5%)" : 5
```

## Token utility

- **Stake** → nhận share phí protocol bằng USDC (real yield, không emission)
- **Lock vePRX** → vote gauge, boost LP rewards 1.5-2.5×, protocol params governance
- **Fee discount** → stake threshold cho giảm 20-50% phí giao dịch
- **Market creation bond** → lock PRX để tạo permissionless market (Phase 2)
- **Oracle dispute** → lock PRX để challenge resolution sai

## Fee distribution — adaptive

Phí thu từ AMM + CLOB + redemption → chia theo growth phase:

```mermaid
flowchart LR
    Fee[Phí protocol] --> Split{Adaptive split}
    Split --> Treasury[Treasury<br/>Dev · Audit · LP gauge]
    Split --> Staker[stkPRX staker<br/>USDC yield]
    Split --> Buyback[Buyback + burn PRX]
    Split --> Insurance[Insurance 5%]

    classDef src fill:#475569,stroke:#334155,color:#fff,stroke-width:1.5px
    classDef sink fill:#2563eb,stroke:#1d4ed8,color:#fff,stroke-width:2px
    classDef burn fill:#dc2626,stroke:#b91c1c,color:#fff,stroke-width:2px
    class Fee src
    class Treasury,Staker,Insurance sink
    class Buyback burn
```

| Phase | Treasury | Staker | Buyback-burn | Insurance |
|---|---|---|---|---|
| Bootstrap | 60% | 20% | 15% | 5% |
| Scale | 25% | 30% | 40% | 5% |
| Mature | 20% | 35% | 40% | 5% |
| Dominance | 15% | 30% | 50% | 5% |

Phase transition qua DAO vote. Detail: [Buyback-burn](buyback-burn.md).

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
| Staking USDC yield, fee discount, lock boost | [Staking real yield](staking-real-yield.md) |
| Lock → vePRX → vote → gauge → bribe | [vePRX & gauge](veprx-gauge.md) |
| Buyback-burn adaptive, treasury | [Buyback-burn](buyback-burn.md) |
| 6-season Points, referral program | [Points & seasons](points-seasons.md) |
| Badge, streak, daily challenge | [Rewards](rewards.md) |
