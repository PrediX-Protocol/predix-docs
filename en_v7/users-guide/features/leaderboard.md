---
description: >-
  Rank traders by performance, follow public profiles, and configure copy
  trading.
---

# Leaderboard & traders

***

Prediction markets have a rare property: trader performance is publicly verifiable. Every position, every fill, every resolution is on-chain. This makes reputation **objectively measurable** rather than self-claimed.

PrediX surfaces this signal across the leaderboard, public trader profiles, follow feeds, and copy trading.

### Leaderboard

> The `/leaderboard` page ranks active traders by five metrics. Sort and filter to find the right traders to learn from or copy.

| Metric             | Description                                   | Update   |
| ------------------ | --------------------------------------------- | -------- |
| **Realized P\&L**  | Profit locked in (USDC)                       | Realtime |
| **Volume**         | Total trade volume                            | Realtime |
| **Win rate**       | % of markets won / total resolved             | Daily    |
| **Accuracy score** | Inverted Brier score (higher = more accurate) | Daily    |
| **Streak**         | Consecutive winning streak                    | Realtime |

Filters:

* **Period**: 24h / 7d / 30d / 90d / all-time
* **Min trades**: 5 / 10 / 50 (filters out accounts that got lucky by chance)
* **Category**: crypto / sports / politics / ...

The **Min trades** filter is the most important quality gate. Without it, leaderboards reward variance over skill.

### Trader profile

Click a trader's name → `/profile/[address]` page.

![Public trader profile: Hero KPIs (Volume, PnL, Win rate, Accuracy) + active positions + trade history + calibration + badges + follow](../../.gitbook/assets/50-trader-profile.svg)

A profile shows hero KPIs (Volume, P\&L, Win rate, Accuracy), active positions, full trade history, calibration chart, badges, and a follow button.

#### <mark style="color:orange;">Public vs private</mark>

By default, trader profiles are **public**:

* Active positions are visible (size, market, side, avg cost).
* History is visible.
* Aggregate stats are shown.

{% hint style="success" %}
Users can **opt out** in [Settings](../../resources/faq.md) **→ Privacy**:

* Hide active positions.
* Hide history.
* Hide identity (anonymous + pseudonym).
{% endhint %}

{% hint style="info" %}
**Note**: Even when hidden, the address is still public on-chain. The app only hides data at the UI level. Technically savvy users can still query the indexer.&#x20;
{% endhint %}

### <mark style="color:orange;">Following traders</mark>

Click **Follow** on any profile. You will receive notifications when the trader:

* Opens a new position above a size threshold you set.
* Closes a large position.
* Reaches a milestone (badge, streak).

A dedicated **Following** feed inside the app aggregates all your followed traders' activity.

### <mark style="color:orange;">Copy trading</mark>

> Copy trading mirrors a lead trader's positions automatically, with risk controls applied per follower.

![Copy trading: follower set config (10% size, cap $100) → lead trades → engine detect → auto/manual mirror → trade mirrored](../../.gitbook/assets/51-copy-trading-flow.svg)

#### Setup copy trading

{% stepper %}
{% step %}
### <mark style="color:orange;">Step 1: Select a Lead Trader</mark>

Browse the platform to find the trader whose strategy you want to replicate.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 2: Configure Your Settings</mark>

Click Copy Trading to open the configuration panel and adjust the following:

* Size %: Define what percentage of the lead trader’s position size you want to mirror.
* Max per Trade: Set an absolute cap (e.g., $100) to limit exposure on any single trade.
* Categories: Filter your risk by selecting only specific categories (e.g., Crypto only).
* Auto vs. Manual: Choose between auto-execute for instant mirroring or confirm each order for manual oversight.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 3: Secure Your Funds</mark>

* Pre-fund USDC into a dedicated Copy Sub-account.

{% hint style="info" %}
Note: Keeping these funds separate from your main wallet helps strictly limit your financial risk.
{% endhint %}
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 4: Activate</mark>

Review your parameters and click Activate to begin following the trader's moves automatically.
{% endstep %}
{% endstepper %}

#### Copy trading risks

{% hint style="warning" %}
**Risks to understand before activating:**

* **Lead trader may underperform later** — past performance does not guarantee future results.
* **Slippage gap**: The lead enters at $0.50; you copy 30s later when the price is already $0.55.
* **Fee accumulation**: Copying many small leads = each lead = 1tx → gas fees add up (significantly reduced if the user qualifies for the sponsor program — applies to both account types; otherwise normal fees apply).&#x20;
{% endhint %}

{% hint style="info" %}
Start small ($50-100) and test for 1 week before scaling.
{% endhint %}

### Trader directory

The `/traders` page — a directory of active traders with filters:

* Sort by metric (same as leaderboard).
* Filter by category, activity period (active in 24h/7d).
* Search by address or pseudonym.

Each trader card shows:

* Avatar (gravatar or custom)
* Pseudonym (self-set) or truncated address
* Top metric (e.g. \~$12k 30d)
* Win rate / Accuracy badge
* Quick actions: Follow / Copy / View profile

### Verified traders

Traders with verified social identity get a verification badge — used as anti-impersonation:

* ENS / Lens / Farcaster verified.
* Twitter linked.
* Optional KYC (for institutional traders).

Verify in [Settings](../../resources/faq.md).

### Privacy & data

* Trader stats are computed from on-chain data → public by default.
* App overlay: pseudonym, avatar, follow graph (off-chain MongoDB).
* If you opt out the app **does not expose** your data, but on-chain data remains public.
* **GDPR / CCPA**: the right to be forgotten applies only to off-chain data (pseudonym, avatar). On-chain data is immutable.

### Anti-sybil

To prevent leaderboard spam from bots:

* **Min activity**: > 5 resolved markets to appear on the leaderboard.
* **Stake gate**: Top 100 leaderboard requires staking >= 100 PRX.
* **Behavior detection**: Wash trading patterns and copy bots are flagged and filtered.

### API

For developers integrating leaderboard or trader data:

```
GET /api/v2/leaderboard?metric=pnl&period=30d&min=10-100
GET /api/v2/users/:address/profile
GET /api/v2/users/:address/follows    (followed by whom)
GET /api/v2/users/:address/following  (following whom)
```

Details: [Backend API](../../developers-guide/api-reference.md#backend-endpoints-v2).
