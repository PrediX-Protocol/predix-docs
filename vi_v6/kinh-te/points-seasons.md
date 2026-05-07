# Points & seasons

PrediX dùng **6-season emission** thay vì airdrop one-time. Community pool = **300M PRX (30%)** trải 3 năm, declining ~30%/season.

## Season overview

| Season | Pool | Timeline | Theme | Conversion | Status |
|---|---|---|---|---|---|
| **S0 Testnet** | 10M | Pre-M1 (2-3 tháng) | Community building · bug testing | Points → PRX via Merkle at TGE | Pre-mainnet |
| **S1 Genesis** | 86M (28.7%) | M1-M6 (FREE) | Mainnet + FIFA WC 2026 | Points → PRX via 3-wave at TGE | Active |
| **S2 Growth** | 60M (20.0%) | M7-M12 | TGE + Exchange listing | Claim via staking | Planned |
| **S3 Scale** | 43M (14.3%) | M13-M18 | Multi-chain + Oracle live | vePRX boost | Planned |
| **S4 Mature** | 30M (10.0%) | M19-M24 | Perp Prediction + Institutional API | Governance-voted | Planned |
| **S5 Expand** | 21M (7.0%) | M25-M30 | Regional expansion APAC+LATAM | DAO-governed | Planned |
| **S6+ Reserve** | 60M (20.0%) | Y3+ | DAO-locked · Emergency · WC 2030 | vePRX governance (51% quorum, 67% approval) | Locked |

S1 lớn nhất vì cold-start = hardest. Early users earn ~20× hơn late joiners. Decay ~30%/season.

## S0 — Testnet

**Pool**: 10M PRX (từ S1 allocation) · **Target**: 50K-100K wallets · **Cap**: 50K PRX/wallet.

Testnet activity được track off-chain (PostgreSQL). Khi mainnet launch: snapshot → tính allocation pro-rata → tạo Merkle tree → publish on-chain. User claim PRX tại TGE bằng Merkle proof.

**Cách earn Points S0** → xem [Points — cách earn PRX](../bat-dau/points.md#s0--testnet).

## S1 — Genesis sub-pool breakdown

86M PRX chia thành 5 sub-pool:

| Sub-pool | PRX | % of S1 | Mục đích |
|---|---|---|---|
| **General Trading** | 56M | 65.1% | Points → PRX cho tất cả mainnet users |
| **KOL & Ambassador** | 12M | 14.0% | Milestone-based, 20% TGE + 80% vesting 6 tháng |
| **Testnet (S0)** | 10M | 11.6% | Reward S0 participants |
| **FIFA Bonus** | 5M | 5.8% | 2.5-3× multiplier M5-M6 |
| **Surprise Reserve** | 3M | 3.5% | Bonus bất ngờ cho genuine users (M3-M4) |

Chi tiết KOL & Ambassador: [KOL & Ambassador program](kol-ambassador.md).

## 3-Wave TGE release

PRX **không phát hết 1 lần** tại TGE. S1 General Trading pool (56M) chia 3 đợt:

| Wave | % | PRX | Timing | Stake boost |
|---|---|---|---|---|
| **Wave 1** | 40% | ~22.4M | TGE (M7) | — |
| **Wave 2** | 30% | ~16.8M | TGE + 30 ngày | Stake W1 → weight ×1.1 |
| **Wave 3** | 30% | ~16.8M | TGE + 60 ngày | Stake W1 + W2 → weight ×1.2 |

**Boost = zero-sum**: staker nhận share lớn hơn từ cùng pool, non-staker nhận ít hơn. Tổng phát ra vẫn đúng — không tạo thêm PRX.

**Tại sao 3-wave**: giảm sell pressure tại TGE. Wave 1 (~22M) thay vì 56M cùng lúc. User stake W1 để boost W2 → giảm bán thêm.

## S2-S5 — Revenue seasons

Từ S2 (M7+), protocol thu phí → phân bổ cho staker yield (USDC), buyback-burn, treasury. Chi tiết: [Buyback-burn](buyback-burn.md) · [Staking real yield](staking-real-yield.md).

Volume milestone rewards (S2-S3):

| Cumulative volume | PRX reward |
|---|---|
| $10,000 | 500 PRX |
| $50,000 | 2,000 PRX |
| $200,000 | 5,000 PRX |
| $500,000 | 10,000 PRX |
| $1,000,000 | 20,000 PRX |

## S6+ Reserve governance

60M PRX DAO-locked. Unlock requires:

- **Quorum**: 51% vePRX supply phải vote
- **Approval**: 67% supermajority
- **Timelock**: 48h delay vote → execution
- **Cap**: max 15M PRX mỗi proposal
- **Cooldown**: 30 ngày giữa các proposals

Tất cả votes on-chain + public dashboard.

**Cách earn Points S0 + S1** → xem [Points — cách earn PRX](../bat-dau/points.md).
