# Notifications & alerts

***

Prediction markets move continuously. Prices shift on resolution news, liquidity flows in and out, and traders open or close positions at any hour.

PrediX delivers a multi-channel notification layer with granular per-type controls — so users receive only the events that matter to them, on the channels they prefer.

### Channels

> PrediX supports five delivery channels covering web, mobile, and external messaging platforms.

![5 notification channels: push browser/mobile, email digest, Discord webhook, Telegram bot, in-app center](../../.gitbook/assets/42-notification-channels.svg)

| Channel             | Realtime    | Setup              | Best for                 |
| ------------------- | ----------- | ------------------ | ------------------------ |
| **In-app**          | Yes         | Default ON         | All users                |
| **Browser push**    | Yes         | Allow permission   | Active web users         |
| **Mobile push**     | Yes         | Install PWA / app  | Mobile-first users       |
| **Email**           | No (digest) | Add email + verify | Passive monitoring       |
| **Discord webhook** | Yes         | Paste URL          | Power users, communities |
| **Telegram bot**    | Yes         | `/connect` command | Primary mobile alerts    |

\{% hint style="info" %\} In-app notifications are ON by default and require no setup. All other channels are opt-in. \{% endhint %\}

### Notification types

PrediX groups notifications into six categories. Each category can be toggled independently per channel — for example, order fills via push, rewards via email only.

#### Trading

Events tied to your orders and open positions.

* **Order fill** — limit order matched (full or partial).
* **Order cancel** — cancelled by you or due to market resolution.
* **Slippage exceeded** — transaction failed due to slippage.
* **Position underwater** — position > $50 with unrealized loss > 20%.

#### Market events

Lifecycle changes on markets where you hold tokens.

* **Resolve** — a market you hold tokens in has resolved.
* **Refund mode enabled** — a market you hold tokens in has entered refund mode.
* **Pause** — a market you hold tokens in has been paused.
* **EndTime warning** — a market you hold tokens in is < 24h, < 1h, or < 10 minutes from endTime.

#### LP

Events for liquidity provider positions.

* **Fee accrued** — uncollected fees exceed $X.
* **Out of range** — a concentrated LP position has moved out of its price range.
* **Pool paused** — pool closed due to market resolution.

#### Social

Interactions from other users.

* **New follower**
* **A trader you follow** opened a large position.
* **Comment / reply** on a market you commented on.
* **Mention** in a discussion.

#### Rewards

Incentive and program milestones.

* **Badge earned**
* **Streak milestone** (7/30/100 days).
* **Weekly PRX distribution** ready to claim.
* **Referral commission** received.

#### Governance

Protocol-level voting and decision events.

* **New vePRX proposal** (if you are a voter).
* **Voting deadline** approaching.
* **New gauge vote epoch**.

### Price alerts

> Custom price triggers per market. Conditions are evaluated every block by the indexer.

![Price alert: user set condition (YES > $0.70) → Indexer check every block → trigger → push + email + in-app](../../.gitbook/assets/43-price-alert-flow.svg)

#### Setup

1. Go to the market detail page → click the bell icon.
2. Choose a condition:
   * **Price above** $X
   * **Price below** $X
   * **Price change** >= Y% within a 2-hour window
   * **Volume spike** >= X% compared to the 24h average
3. Select a channel (push / email / Telegram).
4. Save.

#### Managing alerts

`/settings/alerts` — lists all active alerts.

* Edit, pause, or delete individually.
* **Bulk actions** — delete all alerts for a resolved market.

#### Limits

Active alert capacity scales with PRX stake:

| Tier           | Active alerts |
| -------------- | ------------- |
| Free           | 50            |
| Stake 1k+ PRX  | 200           |
| Stake 10k+ PRX | Unlimited     |

### Notification preferences

`/settings/notifications` — full granular control matrix.

| Type           | In-app | Push | Email | Discord | Telegram |
| -------------- | ------ | ---- | ----- | ------- | -------- |
| Order fill     | Yes    | Yes  | No    | Yes     | Yes      |
| Market resolve | Yes    | Yes  | Yes   | Yes     | Yes      |
| Price alert    | Yes    | Yes  | Yes   | Yes     | Yes      |
| Daily digest   | No     | No   | Yes   | No      | No       |
| Marketing      | No     | No   | No    | No      | No       |

{% hint style="warning" %}
Granular customization per type. **Marketing is OFF by default** — only ON if you explicitly opt in.&#x20;
{% endhint %}

### Email digest

Two digest formats designed for passive monitoring without continuous attention.

#### Daily digest (08:00 local time)

* Portfolio overnight summary (P\&L change).
* Markets approaching endTime in your portfolio.
* Recent activity from traders you follow.
* Top 5 movers (24h price change).
* Rewards earned.

#### Weekly digest (Monday)

* Weekly performance.
* Suggested markets based on your interests.
* Calibration update.
* New features / governance updates.

Unsubscribe link in every email.

### Discord webhook

Route notifications to any Discord channel via webhook — no bot install required.

{% hint style="info" %}
#### Setup

1. In your Discord server → **Settings** → **Integrations** → **Webhooks** → **New**.
2. Copy the URL.
3. Paste into PrediX **Settings** → **Discord webhook**.
4. Test with the **Send test** button.
{% endhint %}

#### <mark style="color:orange;">Notification format</mark>

```
ORDER FILLED
Market: BTC > $500k 2027
Side:   BUY YES
Filled: 100 USDC @ $0.43
P&L:    —
TX:     unichain.xyz/tx/0x...
```

### Telegram bot

Connect via the official PrediX bot for realtime mobile alerts and quick portfolio commands.

1. Open Telegram → search `@predix_alert_bot`.
2. Start a chat → `/connect <wallet_address>`.
3. The app generates a code → paste it into the bot.
4. Done.

#### Commands

* `/portfolio` — quick P\&L summary
* `/alerts` — list active alerts
* `/help` — full command list

### Privacy

\{% hint style="info" %\} **Privacy guarantees:**

* Email and phone are optional. Your address is the primary identifier.
* Notifications are encrypted in transit (TLS).
* Discord/Telegram channels use only the webhook URL — your auth tokens are never stored.
* Unsubscribe = delete data at any time. \{% endhint %\}

### API integration

For developers integrating PrediX notifications into external systems:

```
GET    /api/v2/users/:address/notifications?unread=true
POST   /api/v2/users/:address/notifications/:id/read
POST   /api/v2/users/:address/alerts
DELETE /api/v2/users/:address/alerts/:id
```

Realtime via WebSocket: `wss://api.predix.app/v2/me/notifications` with an auth header.

Details: [Backend API](https://claude.ai/local_sessions/local_014253ac-266f-4a66-a2da-52fef452673c).
