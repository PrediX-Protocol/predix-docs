# Discussion & social feed

Share market insights with the community directly within the app.

## Comments per market

Every market has its own comment thread (the **Discussion** tab on the market detail page).

![Market discussion: comment list (sort newest/top), post comment, reply 2 levels, up/down vote, @mention](../_design/47-discussion-features.svg)

### Features

- **Threaded** 2 levels (comment + reply).
- **Voting** — up/down vote; top comments rise to the top.
- **Mention** `@username` to notify another user.
- **Markdown** support: `**bold**`, `*italic*`, code, link, quote.
- **Image embed** — paste an image URL and the app renders it inline.
- **TX share** — paste a tx hash and the app renders an explorer link + summary.

### Anti-spam

- **Min stake** of 10 PRX (post-TGE) to post.
- **Cooldown** of 30 seconds between comments.
- **Rate limit** of 50 comments / day / user.
- **Report button** — flag inappropriate content.
- **Mod tools** (community moderator role): hide comments, ban users for 24h.

### Trader badges in comments

Usernames in comments display badges:
- Verified (ENS / Lens / Twitter linked)
- Top trader (top 100 leaderboard)
- High accuracy (Brier < 0.15 with > 50 trades)
- PRX whale (stake > 10k PRX)

Helps assess the credibility of a comment.

## Social feed

The `/feed` page — a global activity stream.

![Social feed: on-chain activity (trade, LP, resolve) + off-chain (comment, follow, badge) → filter (following/trending/latest) → realtime cards](../_design/48-social-feed.svg)

### Filters

| Filter | Description |
|---|---|
| **Following** | Activity from users you follow |
| **Trending** | Comments + markets with high engagement in the last 24h |
| **Latest** | Everything, newest first |
| **Markets** | Trade / LP activity only, no social |
| **Discussion** | Comments only, no transactions |

### Card types

- **Trade card**: User X bought $Y YES on market Z. Click → market detail.
- **LP card**: User X provided $Y liquidity to pool Z.
- **Resolve card**: Market Z resolved YES, $Y total volume traded.
- **Comment card**: User X commented on market Z: "...". Click → discussion thread.
- **Badge card**: User X earned the "Prophet" badge (80% accuracy).

## Posts (write-ups)

Users can publish **posts** (Twitter thread-style long-form content):

- Full Markdown support.
- Tag market references (auto-linked).
- Embed charts and images.
- Long-form analysis (e.g. "Why I'm buying YES on the BTC market").

Posts appear in:
- The author's profile (**Posts** tab).
- Followers' feeds.
- Market detail (if a market is tagged).

### Tip jar (TBA Phase 2)

Readers will be able to tip USDC / PRX to the author of a useful post.

## Activity feed (per market)

The **Activity** tab on the market detail page — a real-time activity stream for that specific market:

- Trade ticker (size, price, side).
- Order book changes (large limit orders).
- LP add/remove events.
- New comments.

Useful: monitor hot markets and spot whale moves.

## DM & private chat (TBA)

Phase 2: direct messaging between users. Planned features:
- E2E encryption (XMTP protocol).
- Group chat (DAO subgroups).

## Moderation

PrediX uses a **community moderation** model:

![Moderation: spam detected → user report → community mod review → valid: hide + warn, repeat 3+: ban 7-30 days](../_design/49-moderation-flow.svg)

- **Mod recruitment**: vePRX holders in good standing can apply.
- **Mod compensation**: PRX from treasury.
- **Appeal process**: banned users can appeal via governance.

## Censorship resistance

Comments and posts are stored **off-chain** (MongoDB) for fast UI rendering. A hash of the content is stored **on-chain** (post Phase 2) — proof of existence + censorship resistance.

If PrediX UI hides a comment, the content still exists via the on-chain hash and users can publish independently on IPFS / Arweave.

## Wallet-to-wallet messaging

Phase 2: Integration with **XMTP** — messages between addresses. Decentralized, end-to-end encrypted.

Use cases:
- Negotiate OTC trades.
- Coordinate liquidity provision.
- Private discussions within working groups.

## API

```
GET /api/v2/markets/:id/comments?sort=top|new&limit=50
POST /api/v2/markets/:id/comments  (auth)
GET /api/v2/users/:address/posts
POST /api/v2/posts  (auth)
GET /api/v2/feed?filter=following|trending|latest
GET /api/v2/markets/:id/activity?type=trade|lp|comment
```

Realtime via WebSocket: `wss://api.predix.app/v2/ws/feed`.

Details: [Backend API](../developers/api-reference.md#backend-endpoints-v2).
