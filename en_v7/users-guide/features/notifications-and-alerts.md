---
description: Configure channels, alerts, and digests to stay updated on market activity.
icon: alarm-exclamation
---

# Notifications & Alerts

PrediX delivers notifications across multiple channels: push, email, Discord, Telegram, and in-app - with granular controls for every event type.

### How notifications work

PrediX groups notifications into six categories: **trading**, **market events**, **LP**, **social**, **rewards**, and **governance**. Each category can be toggled independently per channel, so you can receive order fills via push, rewards as a weekly email, and skip the rest.

All notifications respect a strict per-channel preferences matrix. Marketing is **off by default** and only sent if you explicitly opt in.

***

### Delivery Channels

PrediX supports five delivery channels covering web, mobile, and external messaging platforms.

| Channel             | Realtime | Setup                                           | Best for                                   |
| ------------------- | -------- | ----------------------------------------------- | ------------------------------------------ |
| **In-app**          | ✅        | <ul><li>Default ON</li></ul>                    | <ul><li>All users</li></ul>                |
| **Browser push**    | ✅        | <ul><li>Allow permission</li></ul>              | <ul><li>Active web users</li></ul>         |
| **Mobile push**     | ✅        | <ul><li>Install PWA / app</li></ul>             | <ul><li>Mobile-first users</li></ul>       |
| **Email**           | ❌        | <ul><li>Add email + verify</li></ul>            | <ul><li>Passive monitoring</li></ul>       |
| **Discord webhook** | ✅        | <ul><li>Paste URL</li></ul>                     | <ul><li>Power users, communities</li></ul> |
| **Telegram bot**    | ✅        | <ul><li><code>/connect</code> command</li></ul> | <ul><li>Primary mobile alerts</li></ul>    |

{% hint style="info" %}
In-app notifications are ON by default and require no setup. All other channels are opt-in.
{% endhint %}

***

### Notification Types

PrediX groups notifications into six categories. Each category can be toggled independently per channel - for example, order fills via push, rewards via email only.

#### <mark style="color:orange;">1. Trading</mark>

Events tied to your orders and open positions.

* **Order fill** - limit order matched (full or partial).
* **Order cancel** - cancelled by you or due to market resolution.
* **Slippage exceeded** - transaction failed due to slippage.
* **Position underwater** - position > $50 with unrealized loss > 20%.

#### <mark style="color:orange;">2. Market events</mark>

Lifecycle changes on markets where you hold tokens.

* **Resolve** - a market you hold tokens in has resolved.
* **Refund mode enabled** - a market you hold tokens in has entered refund mode.
* **Pause** - a market you hold tokens in has been paused.
* **EndTime warning** - a market you hold tokens in is < 24h, < 1h, or < 10 minutes from endTime.

#### <mark style="color:orange;">3.</mark> <mark style="color:$warning;">Liquidity Provider</mark>

Events for liquidity provider positions.

* **Fee accrued** - uncollected fees exceed $X.
* **Out of range** - a concentrated LP position has moved out of its price range.
* **Pool paused** - pool closed due to market resolution.

#### <mark style="color:orange;">4. Socials</mark>

Interactions from other users.

* **New follower**
* **A trader you follow** opened a large position.
* **Comment / reply** on a market you commented on.
* **Mention** in a discussion.

#### <mark style="color:orange;">5. Rewards</mark>

Incentive and program milestones.

* **Badge earned**
* **Streak milestone** (7/30/100 days).
* **Weekly PRX distribution** ready to claim.
* **Referral commission** received.

#### <mark style="color:orange;">6. Governance</mark>

Protocol-level voting and decision events.

* **New vePRX proposal** (if you are a voter).
* **Voting deadline** approaching.
* **New gauge vote epoch**.

***

### Price Alerts

Price alerts let you set custom triggers on any market. Conditions are evaluated every block by the indexer, and the alert fires the moment the condition becomes true.

<figure><img src="../../.gitbook/assets/15. Price Alerts.png" alt=""><figcaption></figcaption></figure>

<sub>Price alert setting PrediX</sub>

#### Setting up an alert

{% stepper %}
{% step %}
<mark style="color:orange;">**Step 1: Access the Alert Menu**</mark>

* Navigate to the Market Detail Page.
* Click on the Bell Icon to open the notification settings.
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 2: Choose a Condition**</mark>

Select the specific market trigger you want to monitor:

* Price above $X: Triggers when the price rises above your set target.
* Price below $X: Triggers when the price drops below your set target.
* Price change >= Y%: Triggers if the price fluctuates by $$ $Y\%$ $$ or more within a 2-hour window.
* Volume spike >= X%: Triggers if the trading volume increases by $$ $X\%$ $$ compared to the 24h average.
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 3: Select a Notification Channel**</mark>

Choose where you would like to receive your alerts:

* Push: Real-time mobile notifications.
* Email: Direct alerts to your inbox.
* Telegram: Instant messages via the Telegram bot.
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 4: Save and Activate**</mark>

* Review your settings to ensure accuracy.
* Click Save to activate your alert.
{% endstep %}
{% endstepper %}

{% hint style="info" %}
**Managing alerts**

`/settings/alerts` - lists all active alerts.

* Edit, pause, or delete individually.
* **Bulk actions** - delete all alerts for a resolved market.
{% endhint %}

#### Limits

Active alert capacity scales with PRX stake:

| Tier           | Active alerts |
| -------------- | ------------- |
| Free           | 50            |
| Stake 1k+ PRX  | 200           |
| Stake 10k+ PRX | Unlimited     |

***

### Notification Preferences

Visit `/settings/notifications` for the full granular control matrix.

| Type           | In-app | Push | Email | Discord | Telegram |
| -------------- | ------ | ---- | ----- | ------- | -------- |
| Order fill     | ✅      | ✅    | ❌     | ✅       | ✅        |
| Market resolve | ✅      | ✅    | ✅     | Yes     | ✅        |
| Price alert    | ✅      | ✅    | ✅     | ✅       | ✅        |
| Daily digest   | ❌      | ❌    | ✅     | ❌       | ❌        |
| Marketing      | ❌      | ❌    | ❌     | ❌       | ❌        |

{% hint style="info" %}
Granular customization per type. **Marketing is OFF by default** - only ON if you explicitly opt in.
{% endhint %}
