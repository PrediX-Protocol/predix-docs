---
description: Users & Portfolio endpoints — portfolio, positions, orders, trades, PnL, history, profile, social.
---

# Users & Portfolio Endpoints

Mọi endpoint lấy `{address}` làm path param — luôn lowercase 42-char hex.

## Portfolio

### `GET /api/v2/users/{address}/portfolio`

Overview vị thế user: collateral, active positions, tổng PnL.

Response (truncated):

```json
{
  "data": {
    "address": "0xabc...",
    "totalCollateral": { "decimal": "1250.5", ... },
    "activePositions": 12,
    "resolvedWithPayout": 5,
    "totalPnl": { "decimal": "234.5", ... },
    "positions": [
      {
        "marketId": "0x...",
        "marketTitle": { "key": "...", "fallback": "Will BTC > $100K?" },
        "yesBalance": { "decimal": "50", ... },
        "noBalance": { "decimal": "0", ... },
        "avgEntryPrice": "0.48",
        "currentValue": { "decimal": "26", ... },
        "unrealizedPnl": { "decimal": "2", ... }
      }
    ]
  },
  "meta": { ... }
}
```

Cache: **hot (2s)**.

---

### `GET /api/v2/users/{address}/positions/{marketId}`

Drill-down single position.

Cache: **hot (2s)**.

---

## Orders

### `GET /api/v2/users/{address}/orders`

Query: `status` (`open` | `filled` | `cancelled`), `marketId`, `limit`, `offset`.

{% hint style="info" %}
**Không default filter**: không truyền `status` → trả về tất cả. Bug #1 prevention.
{% endhint %}

Response:

```json
{
  "data": [
    {
      "orderId": "0x...",
      "marketId": "0x...",
      "side": "BUY_YES",
      "price": "0.55",
      "amount": { "decimal": "100", ... },
      "filled": { "decimal": "30", ... },
      "remaining": { "decimal": "70", ... },
      "status": "open",
      "createdAt": 1776080000
    }
  ],
  "meta": { "pagination": { ... } }
}
```

Cache: **hot (2s)**.

---

## Trades

### `GET /api/v2/users/{address}/trades`

User's trade history (executed trades), desc timestamp.

Cache: **hot (2s)**.

---

## PnL & Analytics

### `GET /api/v2/users/{address}/pnl`

Query: `period` (`7d` | `30d` | `90d` | `all`).

Response: realized + unrealized PnL breakdown.

Cache: **warm (60s)**.

### `GET /api/v2/users/{address}/history`

Timeline activity (trades, splits, merges, redeems, refunds) kết hợp.

Cache: **hot (2s)**.

### `GET /api/v2/users/{address}/forecasting`

Accuracy metrics — how often user's bet hits.

Cache: **warm (60s)**.

### `GET /api/v2/users/{address}/calibration`

Brier score / calibration curve cho trader — score predictions có reliable không.

Cache: **warm (60s)**.

### `GET /api/v2/users/{address}/accuracy-trend`

Time-series accuracy.

Cache: **warm (60s)**.

---

## Profile

### `GET /api/v2/users/{address}/profile`

Public profile: display name, avatar, bio, stats summary.

Cache: **warm (60s)**.

### `GET /api/v2/auth/me`

Authenticated self profile. Yêu cầu session token.

### `GET /api/v2/auth/me/settings`

User preferences (notifications, language, default slippage).

### `PATCH /api/v2/auth/me/settings`

Update settings.

---

## Achievements & Gamification

### `GET /api/v2/users/{address}/achievements`

Badges earned (mock/whale/trend-setter…).

Cache: **warm (60s)**.

### `GET /api/v2/rewards/boxes`

Available reward boxes (gamified loot boxes).

### `POST /api/v2/rewards/boxes/{boxId}/open`

Open box (yêu cầu auth).

### `GET /api/v2/rewards/challenges`

Active challenges (trade 10 markets this week, win 5 bets…).

### `GET /api/v2/rewards/multiplier`

User's current reward multiplier.

---

## Social (follow graph)

### `GET /api/v2/users/{address}/follows`

List addresses user đang follow.

### `POST /api/v2/users/{address}/follows` (auth)

Follow an address.

### `DELETE /api/v2/users/{address}/follows/{target}` (auth)

Unfollow.

### `GET /api/v2/users/{address}/followers`

List followers.

### `GET /api/v2/traders`

Discover traders (paginated, sortable by volume / accuracy / followers).

### `GET /api/v2/traders/{address}`

Trader profile enriched with leaderboard rank.

### `GET /api/v2/leaderboard`

Query: `period`, `sortBy` (`volume` | `pnl` | `accuracy`).

Cache: **warm (60s)**.

---

## Notifications

### `GET /api/v2/notifications`

User's in-app notifications (event triggered, market resolved, follow events…).

### `GET /api/v2/notifications/unread-count`

Đếm unread — dùng cho bell icon.

### `POST /api/v2/notifications/{id}/read`

Mark one as read.

### `POST /api/v2/notifications/read-all`

Mark all read.

---

## Authorization

Đa số endpoint public read (không cần auth). Endpoint cần auth (yêu cầu `Authorization: Bearer <sessionToken>`):

- `/auth/me`, `/auth/me/settings`, `/auth/logout`
- `POST /notifications/*/read`, `/read-all`
- `POST /users/:address/follows` — chỉ được follow TỪ session's address (address in URL phải match session)
- `POST /rewards/boxes/:id/open`
- Mọi `/admin/*`

Nếu `address` trong URL không match session → 403 Forbidden.
