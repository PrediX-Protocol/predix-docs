---
description: Overview of the PrediX' features available to traders.
---

# Features

This section covers the user-facing features inside the PrediX app — the tools traders use day-to-day to research markets, place trades, manage positions, and engage with the community.

Each feature is documented as its own page with setup steps, configuration options, and developer APIs where relevant.

### What's inside

PrediX features are organized into five core areas:

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-type="image"></th><th data-hidden data-type="number"></th></tr></thead><tbody><tr><td><mark style="color:orange;"><strong>Chart &#x26; timeframe</strong></mark></td><td>Read OHLC charts, switch timeframes, apply indicators, compare markets, and view multi-outcome event overlays.</td><td><a href="chart-timeframe.md">chart-timeframe.md</a></td><td><a href="../../.gitbook/assets/growth (2).png">growth (2).png</a></td><td>null</td></tr><tr><td><mark style="color:orange;"><strong>Notifications &#x26; alerts</strong></mark></td><td>Configure push, email, Discord, and Telegram channels; set custom price alerts; receive daily and weekly digests.</td><td><a href="notifications-and-alerts.md">notifications-and-alerts.md</a></td><td></td><td>null</td></tr><tr><td><mark style="color:orange;"><strong>Discussion &#x26; social</strong></mark></td><td>Comment on markets, follow a global activity feed, publish long-form posts, and participate in community moderation.</td><td><a href="discussion.md">discussion.md</a></td><td></td><td>null</td></tr><tr><td><mark style="color:orange;"><strong>Leaderboard &#x26; traders</strong></mark></td><td>Rank traders by P&#x26;L, volume, accuracy, and streak; follow public profiles; configure copy trading.</td><td><a href="leaderboard.md">leaderboard.md</a></td><td></td><td>null</td></tr><tr><td><mark style="color:orange;"><strong>Multi-outcome events</strong></mark></td><td>Trade questions with N possible outcomes (tournament winners, election candidates) under an on-chain mutual-exclusion constraint.</td><td><a href="multi-outcome.md">multi-outcome.md</a></td><td></td><td>null</td></tr></tbody></table>



### Design principles

Three principles shape how features are built:

* <mark style="color:orange;">**On-chain by default**</mark> — all trading, positions, and resolution data live on-chain. Off-chain layers (social, notifications) are additive overlays, not sources of truth.
* <mark style="color:orange;">**Granular user control**</mark> — every notification type, every privacy setting, every alert is opt-in and configurable per channel.
* <mark style="color:orange;">**Composable**</mark> — outcome tokens are ERC-20 and event structures are queryable on-chain, so external apps can build on top of PrediX features.

### Where to start

<details>

<summary><mark style="color:orange;"><strong>New to the app?</strong></mark> </summary>

&#x20;Open [**Chart & timeframe**](chart-timeframe.md) to learn how to read a prediction market chart.

</details>

<details>

<summary><mark style="color:orange;"><strong>Want to be notified when something happens?</strong></mark> </summary>

Go to [**Notifications & alerts**](notifications-and-alerts.md).

</details>

<details>

<summary><mark style="color:orange;"><strong>Looking for traders to learn from or copy?</strong></mark> </summary>

See [**Leaderboard & traders**](leaderboard.md).

</details>

<details>

<summary><mark style="color:orange;"><strong>Trading a tournament or election?</strong></mark> </summary>

See [**Multi-outcome events**](multi-outcome.md).

</details>

<details>

<summary><mark style="color:orange;"><strong>Want to discuss markets with other traders?</strong></mark> </summary>

&#x20;See [**Discussion & social**](discussion.md).

</details>
