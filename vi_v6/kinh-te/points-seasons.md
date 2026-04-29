# Points & seasons

PrediX dùng **6-season emission** thay vì airdrop one-time. Community pool = **300M PRX (30%)** trải 3 năm, declining ~30%/season.

## 6-Season overview

| Season | Pool | Timeline | Theme |
|---|---|---|---|
| **S1 Genesis** | 86M (28.7%) | M1-M6 | Mainnet launch · Points → PRX at TGE |
| **S2 Growth** | 60M (20.0%) | M7-M12 | Fee ON · staking rewards |
| **S3 Scale** | 43M (14.3%) | M13-M18 | Multi-chain · market creation |
| **S4 Mature** | 30M (10.0%) | M19-M24 | DAO governance |
| **S5 Expand** | 21M (7.0%) | Y3 H1 | Regional expansion |
| **S6+ Reserve** | 60M (20.0%) | Y3+ | DAO-locked |

S1 lớn nhất vì cold-start = hardest. Early users earn nhiều hơn late joiners. S6 Reserve chỉ unlock qua vePRX governance vote.

## S1 — Points system

Free period (M1-M6) — mọi action earn Points. Convert Points → PRX at TGE:

```
Your PRX = (Your Points ÷ Total S1 Points) × 86M PRX
```

| Activity | Points |
|---|---|
| Sign up | 100 |
| First trade | 50 |
| Per $100 volume | 10 |
| Refer friend (friend trades ≥1×) | 200 |
| Provide liquidity ≥$100 | 150 |
| Oracle vote | 50 |
| 7-day streak | 30 |

S2-S6 pools separate — unspent S1 không carry over.

## Referral program — 2 phase

**Phase 1 (M1-M6, free period)**: Points referral.

| Action | Referrer | Referred |
|---|---|---|
| Refer (standard) | 200 Points | 100 Points |
| Ref makes first trade | +50 bonus | +50 |

Ongoing: referrer earn **10% of referred user's Points**.

**Phase 2 (M7+, fee ON)**: Referral commission từ phí protocol. Tỷ lệ cụ thể sẽ công bố pre-launch.

Transition M6 → M7: same referral link, old refs auto-migrate.

## Anti-gaming

- Sybil detection (address clustering, behavior analysis)
- Wash trade auto-decay (mua + bán ngay → Points giảm)
- Cap per wallet (max Points/day)
- Min stake post-TGE để earn reward

## Track

App UI: **Profile → Points** tab — current Points, rank, estimated PRX.
