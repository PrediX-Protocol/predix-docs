# Buyback-burn & treasury

PrediX mua PRX từ thị trường bằng phí protocol + burn vĩnh viễn. % buyback adaptive theo growth phase (15% bootstrap → 50% dominance).

## Cơ chế

![Buyback-burn: mỗi tuần protocol collect fees USDC → swap USDC→PRX (random timing, split tx) → burn 0x...dEaD → supply giảm vĩnh viễn](../_design/05-buyback-burn.svg)

Event `BuybackExecuted(usdcSpent, prxBurned)` emit on-chain. Burn không reversible.

## Adaptive fee split

% phân bổ thay đổi theo phase — bootstrap ưu tiên treasury runway, mature shift sang buyback dominant:

| Phase | Treasury | Staker | Buyback | Insurance |
|---|---|---|---|---|
| **Bootstrap** | 60% | 20% | 15% | 5% |
| **Scale** | 25% | 30% | 40% | 5% |
| **Mature** | 20% | 35% | 40% | 5% |
| **Dominance** | 15% | 30% | 50% | 5% |

Phase transition qua **DAO vote** (vePRX governance).

## Net deflationary

PRX trở nên **net deflationary** khi burn/năm > emission/năm. Emission ≈ 0 sau 4 năm vest xong → post-Y4 net deflationary nếu volume duy trì.

## Insurance fund — 5%

5% protocol revenue → insurance treasury trên **mọi phase**. Coverage: partial reimbursement nếu contract exploit. Payout chỉ qua DAO vote, không auto.

## Treasury

Treasury fund 4 use case:

| Use case | Detail |
|---|---|
| **Dev funding** | Team post-vest, grants contributor, hackathon |
| **Audit** | External audit firms, ≥ 1 round/year |
| **LP subsidy** | Gauge voting — pool được vePRX vote nhận subsidy |
| **Insurance top-up** | Bổ sung insurance fund khi cần |

Quản lý:
- On-chain multisig 3/5 (Gnosis Safe)
- Spend > $10k → governance vote
- Quarterly report public

## Track

Public dashboard:
- Weekly buyback amount + PRX burned
- Cumulative burn since TGE
- Treasury balance + spend history
- Insurance fund balance
