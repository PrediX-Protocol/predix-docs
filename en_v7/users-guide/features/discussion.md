---
description: Comment on markets, follow the activity feed, and engage with the community.
icon: share-nodes
---

# Social & Discussion

PrediX embeds social features directly into the trading interface - comment threads on every market, a global activity feed, long-form posts, and community moderation. Discussion lives next to the market it shapes, not in an external forum.

### Comments per market

> Every market has its own comment thread, accessed via the **Discussion** tab on the market detail page. Comments are threaded two levels deep - a top-level comment with one level of replies - and ranked by upvotes so the strongest argument rises to the top.

#### <mark style="color:orange;">1. Features</mark>

* **Threaded** 2 levels (`comment` + `reply`).
* **Voting** - `up vote`/`down vote`; top comments rise to the top.
* **Image embed** - paste an image URL and the app renders it inline.
* **TX share** - paste a `txhash` and the app renders an explorer `link` + `summary`.

#### <mark style="color:orange;">2. Anti-spam</mark>

PrediX enforces anti-spam through an **economic gate** rather than CAPTCHA alone:

* **Min stake** of 10 PRX (post-TGE) to post.
* **Cooldown** of 30 seconds between comments.
* **Rate limit** of 50 comments / day / user.
* **Report button** - flag inappropriate content.
* **Mod tools** (community moderator role): hide comments, ban users for 24h.

The 10 PRX stake makes spam economically unprofitable while keeping the barrier low for genuine participants.

#### <mark style="color:orange;">3. Trader badges in comments</mark>

Usernames in comments display badges that surface the commenter's track record:

* **Verified** - ENS / Lens / Twitter linked.
* **Top trader** - top 100 leaderboard.
* **High accuracy** - Brier < 0.15 with > 50 trades.
* **PRX whale** - stake > 10k PRX.

Badges help readers weigh the credibility of an argument before acting on it.

***

### Social feed

> The `/feed page` is a global activity stream that merges on-chain transactions with off-chain social actions into a single timeline.

#### <mark style="color:orange;">1. Filters</mark>

| Filter         | Description                                                               |
| -------------- | ------------------------------------------------------------------------- |
| **Following**  | <ul><li>Activity from users you follow</li></ul>                          |
| **Trending**   | <ul><li>Comments + markets with high engagement in the last 24h</li></ul> |
| **Latest**     | <ul><li>Everything, newest first</li></ul>                                |
| **Markets**    | <ul><li>Trade / LP activity only, no social</li></ul>                     |
| **Discussion** | <ul><li>Comments only, no transactions</li></ul>                          |

#### <mark style="color:orange;">2. Card types</mark>

Feed items render as typed cards, each linking back to its source object:

* **Trade card** - _User X bought $Y YES on market Z._ Click → market detail.
* **LP card** - _User X provided $Y liquidity to pool Z._
* **Resolve card** - _Market Z resolved YES, $Y total volume traded._
* **Comment card** - _User X commented on market Z: "..."_ Click → discussion thread.
* **Badge card** - _User X earned the "Prophet" badge (80% accuracy)._

***

### Long-form Posts

Beyond short comments, users can publish **posts** - Twitter thread-style long-form content for deeper analysis:

* Full Markdown support.
* Tag market references (auto-linked).
* Embed charts and images.
* Long-form analysis (e.g. _"Why I'm buying YES on the BTC market"_).

Posts appear in three places:

* The author's profile (**Posts** tab).
* Followers' feeds.
* Market detail (if a market is tagged).

{% hint style="info" %}
**Tip jar (TBA - Phase 2)**

Readers will be able to tip USDC / PRX to the author of a useful post.
{% endhint %}

***

### Per-market activity feed

The **Activity** tab on the market detail page is a realtime activity stream scoped to that specific market:

* Trade ticker (size, price, side).
* Order book changes (large limit orders).
* LP add/remove events.
* New comments.

Useful for monitoring hot markets and spotting whale moves as they happen.

***

### Moderation

PrediX uses a **community moderation** model - moderators are recruited from vePRX holders in good standing, not appointed centrally.

#### Content Moderation & Penalty Process

{% stepper %}
{% step %}
<mark style="color:orange;">**Step 1: User Reporting**</mark>

* The process begins when a user flags and reports specific content for review.
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 2: Moderator Review**</mark>

* Our moderation team manually evaluates the reported content against community guidelines to determine its validity.
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 3: Initial Enforcement**</mark>

If the report is found to be valid:

* The violating content is immediately hidden from public view.
* The author receives a formal warning.
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 4: Escalation & Banning**</mark>

The system tracks recurring violations:

* 3+ repeat offenses will trigger an automatic escalation.
* The account will face a temporary ban ranging from 7 to 30 days, depending on the severity.
{% endstep %}
{% endstepper %}

> **Note**:
>
> * **Mod recruitment** - vePRX holders in good standing can apply.
> * **Mod compensation** - PRX from treasury.
> * **Appeal process** - banned users can appeal via governance.

***

### Censorship Resistance

Comments and posts are stored **off-chain** in MongoDB for fast UI rendering. Starting Phase 2, a hash of every comment is also stored **on-chain** as proof of existence.

{% hint style="info" %}
If PrediX UI hides a comment, the original content still exists via the on-chain hash. Users can verify and re-publish it independently on IPFS or Arweave.
{% endhint %}

***

### Phase 2 - TBA

<details>

<summary><strong>Direct Messaging</strong></summary>

Direct messaging between users. Planned features:

* E2E encryption (XMTP protocol).
* Group chat (DAO subgroups).

</details>

<details>

<summary><strong>Wallet-to-wallet Messaging</strong></summary>

Phase 2 will integrate **XMTP** — decentralized, end-to-end encrypted messaging between wallet addresses. Planned use cases include:

* Negotiate OTC trades.
* Coordinate liquidity provision.
* Private discussions within working groups.

</details>

***

### API Intergration

For developers building social integrations or external clients:

```
GET  /api/v1/markets/:id/comments?sort=top&limit=50
POST /api/v1/markets/:id/comments        (auth)
GET  /api/v1/users/:address/posts
POST /api/v1/posts                       (auth)
GET  /api/v1/feed?filter=following|trending|latest
POST /api/v1/markets/:id/activity?type=trade|comment
```

Realtime via WebSocket: `wss://api.predix.app/v2/me/feed`.

{% hint style="info" %}
Details: [Backend API](/broken/pages/NaTE7epVHDvl0K9Pol1r#backend-endpoints-v2).
{% endhint %}
