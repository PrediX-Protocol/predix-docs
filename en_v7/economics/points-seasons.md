---
icon: money-bill-trend-up
---

# Points & Seasons

PrediX uses **6-season emission** instead of a one-time airdrop. Community pool = **300M PRX (30%)** spread over 3 years, declining \~30%/season.

## Season overview

| Season          | Pool        | Timeline            | Theme                               | Conversion                                  | Status      |
| --------------- | ----------- | ------------------- | ----------------------------------- | ------------------------------------------- | ----------- |
| **S0 Testnet**  | 10M         | Pre-M1 (2-3 months) | Community building - bug testing    | Points -> PRX via Merkle at TGE             | Pre-mainnet |
| **S1 Genesis**  | 86M (28.7%) | M1-M6 (FREE)        | Mainnet + FIFA WC 2026              | Points -> PRX via 3-wave at TGE             | Active      |
| **S2 Growth**   | 60M (20.0%) | M7-M12              | TGE + Exchange listing              | Claim via staking                           | Planned     |
| **S3 Scale**    | 43M (14.3%) | M13-M18             | Multi-chain + Oracle live           | vePRX boost                                 | Planned     |
| **S4 Mature**   | 30M (10.0%) | M19-M24             | Perp Prediction + Institutional API | Governance-voted                            | Planned     |
| **S5 Expand**   | 21M (7.0%)  | M25-M30             | Regional expansion APAC+LATAM       | DAO-governed                                | Planned     |
| **S6+ Reserve** | 60M (20.0%) | Y3+                 | DAO-locked - Emergency - WC 2030    | vePRX governance (51% quorum, 67% approval) | Locked      |

S1 is the largest because cold-start is the hardest. Early users earn \~20x more than late joiners. Decay is \~30%/season.

> **Note**: S0's 10M is carved **from** S1's 86M allocation (not additive). Total community pool = 300M.

## S0 - Testnet

**Pool**: 10M PRX (from S1 allocation) - **Target**: 50K-100K wallets - **Cap**: 50K PRX/wallet.

Testnet activity is tracked off-chain (PostgreSQL). At mainnet launch: snapshot -> calculate allocation pro-rata -> generate Merkle tree -> publish on-chain. Users claim PRX at TGE using a Merkle proof.

**How to earn Points S0** -> see [Points — how to earn PRX](incentive-and-community/points.md#s0--testnet).

## S1 - Genesis sub-pool breakdown

86M PRX split into 5 sub-pools:

| Sub-pool             | PRX | % of S1 | Purpose                                              |
| -------------------- | --- | ------- | ---------------------------------------------------- |
| **General Trading**  | 56M | 65.1%   | Points -> PRX for all mainnet users                  |
| **KOL & Ambassador** | 12M | 14.0%   | Milestone-based, 20% TGE + 80% vesting over 6 months |
| **Testnet (S0)**     | 10M | 11.6%   | Reward S0 participants                               |
| **FIFA Bonus**       | 5M  | 5.8%    | 2.5-3x multiplier M5-M6                              |
| **Surprise Reserve** | 3M  | 3.5%    | Surprise bonus for genuine users (M3-M4)             |

KOL & Ambassador details: [KOL & Ambassador program](kol-ambassador.md).

## 3-Wave TGE release

PRX is **not distributed all at once** at TGE. The S1 General Trading pool (56M) is split into 3 waves:

| Wave       | %   | PRX     | Timing        | Stake boost                  |
| ---------- | --- | ------- | ------------- | ---------------------------- |
| **Wave 1** | 40% | \~22.4M | TGE (M7)      | —                            |
| **Wave 2** | 30% | \~16.8M | TGE + 30 days | Stake W1 -> weight x1.1      |
| **Wave 3** | 30% | \~16.8M | TGE + 60 days | Stake W1 + W2 -> weight x1.2 |

**Boost is zero-sum**: stakers receive a larger share from the same pool; non-stakers receive less. The total distributed remains the same — no additional PRX is created.

**Why 3 waves**: reduces sell pressure at TGE. Wave 1 (\~22M) instead of 56M all at once. Users who stake W1 to boost W2 -> further reduces selling.

## S2-S5 - Revenue seasons

Starting from S2 (M7+), the protocol collects fees and allocates them to staker yield (USDC), buyback-burn, and treasury. Details: [Buyback-burn](buyback-burn.md) - [Staking](staking.md).

Volume milestone rewards (S2-S3):

| Cumulative volume | PRX reward |
| ----------------- | ---------- |
| $10,000           | 500 PRX    |
| $50,000           | 2,000 PRX  |
| $200,000          | 5,000 PRX  |
| $500,000          | 10,000 PRX |
| $1,000,000        | 20,000 PRX |

## S6+ Reserve governance

60M PRX DAO-locked. Unlock requires:

* **Quorum**: 51% vePRX supply must vote
* **Approval**: 67% supermajority
* **Timelock**: 48h delay from vote to execution
* **Cap**: max 15M PRX per proposal
* **Cooldown**: 30 days between proposals

All votes are on-chain with a public dashboard.

**How to earn Points S0 + S1** -> see [Points — how to earn PRX](incentive-and-community/points.md).
