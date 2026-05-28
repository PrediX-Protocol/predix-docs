---
icon: cricket-bat-ball
---

# Rewards & Gamification

Long-term activity rewards (badges, streaks, daily challenges, reward boxes). Independent of the Season pool — engagement-driven, throughout the protocol's lifetime.

> Points / season-based emission / referral 2-phase: see [Points & seasons](points-seasons.md).

<figure><img src="../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

<sub>4 reward types: Streak (daily login, win streak) + Badge (NFT mi</sub>

<sub>lestone) + Daily Challenge (3 random/day) + Reward Box (5% drop, VRF random) -> convert to PRX/USDC/NFT</sub>

## 1. Streaks — action chains

| Streak type        | Criteria                           | Reward                                            |
| ------------------ | ---------------------------------- | ------------------------------------------------- |
| **Daily login**    | Log in every consecutive day       | 5 / 10 / 25 / 50 Points @ 7 / 30 / 100 / 365 days |
| **Win streak**     | Consecutive winning trades (> $10) | Multiplier 1.1x -> 2.0x points for the next trade |
| **Trading streak** | Trade >= 1 per day                 | Daily bonus + badge                               |

Missing a day breaks the streak. Restarts from 0.

## 2. Badges — NFT milestones

NFT badges (ERC-1155 on Unichain) earned upon reaching milestones:

<figure><img src="../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

<sub>Badge categories: Volume ($1K-$100K), Accuracy (70%-90% win rate), Streak (7-365 day), Special (beta tester, community hero, event winner)</sub>



* Badges are NFTs — transferable (until locked).
* Displayed on profile and leaderboard.
* Some badges are **locked** (non-transferable) to prevent wash trading.
* Rare badges may serve as collateral for lending in Phase 2.

## 3. Daily challenges

3 random challenges each day:

| Example challenge                    | Reward                                                            |
| ------------------------------------ | ----------------------------------------------------------------- |
| Trade volume >= $50 today            | 100 Points                                                        |
| Place 1 limit order                  | 50 Points                                                         |
| Hold a position for at least 4 hours | 75 Points                                                         |
| Try 3 different markets              | 100 Points                                                        |
| Refer 1 friend                       | see [Points](incentive-and-community/points.md) (2-gate referral) |

Resets at 00:00 UTC daily.

## 4. Reward boxes — random drops

Sealed boxes open at resolution, containing random PRX / USDC / NFT.

<figure><img src="../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

<sub>Reward box: eligible trade -> 5% drop chance -> box sealed -> user opens -> Chainlink VRF random -> 80% PRX / 15% USDC / 5% rare NFT</sub>



* **Drop rate**: \~5% per trade > $10.
* **Pool**: 80% PRX, 15% USDC, 5% rare NFT.
* **Range**: 1-1000 PRX (median \~10), $0.10-$50 USDC (median $1), NFT special edition.
* **Randomness**: Chainlink VRF — verifiable, cannot be manipulated.

## Converting points to PRX

**S1 (M1-M6, free period)**: Points convert at TGE. See [Points & seasons — S1 Genesis](points-seasons.md#s1--genesis-sub-pool-breakdown).

**Post-TGE**: Points from activity rewards (streaks, daily challenges) convert weekly:

```
Your weekly PRX = (your weekly points / total weekly points) × weekly PRX pool
```

Weekly pool funded from the Season pool (S2-S6 active) or Treasury budget (governance vote).

Claim manually or auto-compound (re-stake into vault).

## Anti-sybil

To prevent bot farming of rewards:

* **Min stake post-TGE**: Accounts must stake >= 10 PRX to earn rewards above a threshold.
* **Verification**: Email + (optional) phone reduces rate for unverified accounts.
* **Behavior pattern**: Wash trade detector — rapid buy + sell patterns reduce or zero out volume points.
* **Cap per wallet**: Tier rewards have an absolute cap (e.g. max 10,000 pts/day).
* **Random snapshot**: Occasional snapshots at non-standard times to prevent last-minute gaming.

## Roadmap (TBA)

* Tournament mode (weekly competition, prize pool)
* Quest lines by theme (crypto, sports, politics)
* Guild system (group rewards)
