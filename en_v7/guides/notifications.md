# Notifications & alerts

Stay updated on important markets and your portfolio activity.

## Channels

![5 notification channels: push browser/mobile, email digest, Discord webhook, Telegram bot, in-app center](../.gitbook/assets/42-notification-channels.svg)

| Channel             | Realtime    | Setup              | Best for                 |
| ------------------- | ----------- | ------------------ | ------------------------ |
| **In-app**          | Yes         | Default ON         | All users                |
| **Browser push**    | Yes         | Allow permission   | Active web users         |
| **Mobile push**     | Yes         | Install PWA / app  | Mobile-first users       |
| **Email**           | No (digest) | Add email + verify | Passive monitoring       |
| **Discord webhook** | Yes         | Paste URL          | Power users, communities |
| **Telegram bot**    | Yes         | `/connect` command | Primary mobile alerts    |

## Notification types

### Trading

* **Order fill** — limit order matched (full or partial).
* **Order cancel** — cancelled by you or due to market resolution.
* **Slippage exceeded** — transaction failed due to slippage.
* **Position underwater** — position > $50 with unrealized loss > 20%.

### Market events

* **Resolve** — a market you hold tokens in has resolved.
* **Refund mode enabled** — a market you hold tokens in has entered refund mode.
* **Pause** — a market you hold tokens in has been paused.
* **EndTime warning** — a market you hold tokens in is < 24h, < 1h, or < 10 minutes from endTime.

### LP

* **Fee accrued** — uncollected fees exceed $X.
* **Out of range** — a concentrated LP position has moved out of its price range.
* **Pool paused** — pool closed due to market resolution.

### Social

* **New follower**.
* **A trader you follow** opened a large position.
* **Comment / reply** on a market you commented on.
* **Mention** in a discussion.

### Rewards

* **Badge earned**.
* **Streak milestone** (7/30/100 days).
* **Weekly PRX distribution** ready to claim.
* **Referral commission** received.

### Governance

* **New vePRX proposal** (if you are a voter).
* **Voting deadline** approaching.
* **New gauge vote epoch**.

## Price alerts

![Price alert: user set condition (YES > $0.70) → Indexer check every block → trigger → push + email + in-app](../.gitbook/assets/43-price-alert-flow.svg)

### Setup

1. Go to the market detail page → click the bell icon.
2. Choose a condition:
   * **Price above** $X
   * **Price below** $X
   * **Price change** >= Y% within a Z-hour window
   * **Volume spike** >= X% compared to the 24h average
3. Select a channel (push / email / Telegram).
4. Save.

### Managing alerts

`/settings/alerts` — lists all active alerts.

* Edit, pause, or delete.
* Bulk actions (delete all alerts for a resolved market).

### Limits

* Free tier: 50 active alerts.
* Stake 1k+ PRX: 200 alerts.
* Stake 10k+ PRX: unlimited.

## Notification preferences

`/settings/notifications`:

| Type           | In-app | Push | Email | Discord | Telegram |
| -------------- | ------ | ---- | ----- | ------- | -------- |
| Order fill     | Yes    | Yes  | No    | Yes     | Yes      |
| Market resolve | Yes    | Yes  | Yes   | Yes     | Yes      |
| Price alert    | Yes    | Yes  | No    | Yes     | Yes      |
| Daily digest   | No     | No   | Yes   | No      | No       |
| Marketing      | No     | No   | No    | No      | No       |

Granular customization per type. **Marketing is OFF by default** — only ON if you explicitly opt in.

## Email digest

Daily digest (08:00 local time):

* Portfolio overnight summary (P\&L change).
* Markets approaching endTime in your portfolio.
* Recent activity from traders you follow.
* Top 5 movers (24h price change).
* Rewards earned.

Weekly digest (Monday):

* Weekly performance.
* Calibration update.
* Suggested markets based on your interests.
* New features / governance updates.

Unsubscribe link in every email.

## Discord webhook

Setup:

1. In your Discord server → Settings → Integrations → Webhooks → New.
2. Copy the URL.
3. Paste into PrediX Settings → Discord webhook.
4. Test with the **Send test** button.

Notification format:

```
Order filled
Market: BTC > $100k 2027
Side: BUY YES
Filled: 100 USDC @ $0.48
P&L: -
TX: uniscan.xyz/tx/0x...
```

## Telegram bot

1. Open Telegram → search `@predix_alert_bot`.
2. Start a chat → `/connect <wallet_address>`.
3. The app generates a code → paste it into the bot.
4. Done.

Commands:

* `/portfolio` — quick P\&L summary
* `/alerts` — list active alerts
* `/help` — full command list

## Privacy

* Email and phone are optional. Your address is the primary identifier.
* Notifications are encrypted in transit (TLS).
* Discord/Telegram channels use only the webhook URL — your auth tokens are never stored.
* Unsubscribe + delete data at any time.

## API integration

```
GET /api/v2/users/:address/notifications?unread=true
POST /api/v2/users/:address/notifications/:id/read
GET /api/v2/users/:address/alerts
POST /api/v2/users/:address/alerts
DELETE /api/v2/users/:address/alerts/:id
```

Realtime via WebSocket: `wss://api.predix.app/v2/ws/notifications` with an auth header.

Details: [Backend API](../developers/api-reference.md#backend-endpoints-v2).
