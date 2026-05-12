# Points — How to Earn PRX

PrediX rewards users with **Points** — a unit measuring contribution. Points convert into **PRX tokens** on a pro-rata basis. The earlier you participate, the more you earn.

## S0 — Testnet (Ongoing)

**Pool: 10M PRX** · 2-3 months before mainnet · Cap 50K PRX/wallet.

| ID | Activity | Points | Condition | Frequency |
|---|---|---|---|---|
| Q0.1 | Connect wallet | 5 | Once | Once |
| Q0.2 | First testnet trade | 30 | Any market | Once |
| Q0.3 | 10 testnet trades | 80 | Different market/day | Once |
| Q0.4 | 50 testnet trades | 150 | ≥5 markets, ≥7 days | Once |
| Q0.5 | LP ≥1,000 testUSDC | 60 | Hold LP ≥72h | Once |
| Q0.6 | Bug report (verified) | 200-500 | Team verified + severity | Per bug |
| Q0.7 | X Follow + RT + Tag | 15 | Proof on Google Drive | Once |
| Q0.8 | Create content | 100-300 | URL + community vote | Per content |
| Q0.9 | Referral (testnet) | 30/ref | Ref makes ≥5 trades, cap 50 refs | Ongoing |
| Q0.10 | 7-day streak | 25 | 1 trade/day | Weekly |

**Conversion**: S0 Points → PRX via Merkle proof at TGE. Formula: `(Your Points / Total S0 Points) x 10M PRX`, cap 50K PRX/wallet.

## S1 — Genesis (Mainnet)

**Pool: 56M PRX** (general trading) · M1-M6 · **Free period** — no trading fees.

| ID | Activity | Points | Condition | Frequency |
|---|---|---|---|---|
| Q1.1 | Connect wallet | 10 | Once | Once |
| Q1.2 | First real-money trade | 100 | Min $10 USDC | Once |
| Q1.3 | Per $100 volume | 10 | Any market | Ongoing |
| Q1.4 | Referral — friend connects wallet | 50 | Cap 100 refs | Ongoing |
| Q1.5 | Referral — friend trades ≥$10 | 100 | Funded wallet gate | Ongoing |
| Q1.6 | Provide LP ≥$100 | 150 | Hold LP ≥7 days | Once |
| Q1.7 | 7-day streak | 50 | 1 trade/day min $5 | Weekly |
| Q1.8 | 14-day streak (bonus) | 120 | Extends 7-day streak | Bi-weekly |
| Q1.9 | Win streak — 3 correct | 30 | 3 correct predictions | Ongoing |
| Q1.10 | Market diversity | 20/market | ≥$50/new market, cap 10 | Ongoing |

**2-gate referral**: Referring a friend requires **2 steps**. Step 1: friend connects wallet (+50 pts). Step 2: friend trades ≥$10 (+100 pts). Both steps must be completed.

### FIFA World Cup Boost (M5-M6)

During months 5-6 (FIFA WC), Points are **boosted 2.5-3x**:

| Activity | Normal (M1-M4) | FIFA boost (M5-M6) |
|---|---|---|
| Refer a friend | 50 + 100 Points | **125 + 250 Points (2.5x)** |
| Being referred | 100 Points | **300 Points (3x)** |
| Referred friend trades $500+ during FIFA | — | **+200 referrer / +100 referred** |
| Tier 2 referral (indirect) | — | **+100 Points** |

### Converting Points → PRX

```
PRX received = (Your Points / Total S1 Points) x 56,000,000 PRX
```

- **Pro-rata**: The more participants, the fewer PRX each Point yields.
- **3-Wave release**: PRX is not distributed all at once. Wave 1 (40%) at TGE, Wave 2 (30%) at TGE+30d, Wave 3 (30%) at TGE+60d. Stake Wave 1 → boost Wave 2 (x1.1). Stake both W1+W2 → boost Wave 3 (x1.2).
- **No carry-over**: S0 / S1 Points are separate. Each Season = separate pool.
- **Cap**: 100K PRX/wallet (S1).

Details on 3-Wave + season roadmap: [Points & seasons](../economics/points-seasons.md).

## Anti-Gaming

- **Cost gate**: S1 first trade min $10 USDC — blocks free sybil accounts
- **2-gate referral**: Reward only when referee funds + trades ≥$10
- **Wash trade detection**: Volume from counterparty overlap is flagged
- **Duration lock**: LP must hold ≥7 days (S1) — no flash deposits
- **Cap per wallet**: Max Points/day + max 100 refs/wallet

## Tracking Points

App UI: **Profile → Points** tab.
- Current Points + quest progress
- Season rank
- Estimated PRX (pool / total Points)
- Referral tree + earnings

## KOL & Ambassador

If you're a content creator (≥1K followers): join the [KOL & Ambassador program](../economics/kol-ambassador.md) — separate pool of 12M PRX, milestone-based.
