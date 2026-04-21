---
description: Events endpoints — multi-outcome markets (Polymarket-style event groups).
---

# Events Endpoints

Event = nhóm N binary market liên kết với mutual-exclusion resolution. Xem [EventFacet](../smart-contracts/event-facet.md) cho cơ chế on-chain.

## `GET /api/v2/events` — List

Query:

| Param | Kiểu | Mô tả |
|---|---|---|
| `status` | enum | `active` \| `resolved` \| `refunding` |
| `categoryId` | string | |
| `tag` | string | |
| `q` | string | Full-text search |
| `sortBy` | enum | `newest` \| `volume` \| `ending_soon` |
| `limit` | int | ≤100 |
| `offset` | int | |

Response:

```json
{
  "data": [
    {
      "eventId": "42",
      "name": { "key": "event.42.name", "fallback": "Ai thắng World Cup 2026?" },
      "status": "active",
      "endTime": 1793664000,
      "childMarketCount": 32,
      "totalVolume": { "decimal": "150000", ... },
      "featured": true,
      "winningIndex": null,
      "capabilities": ["trade"]
    }
  ],
  "meta": { "pagination": { ... } }
}
```

Cache: **hot (2s)**.

---

## `GET /api/v2/events/{id}` — Detail

Response: full event envelope với `members[]` — array các child markets:

```json
{
  "data": {
    "eventId": "42",
    "name": { "key": "...", "fallback": "Ai thắng World Cup 2026?" },
    "description": { "key": "...", "fallback": "..." },
    "endTime": 1793664000,
    "status": "active",
    "creator": "0x...",
    "oracle": "0x...",
    "winningIndex": null,
    "winningChildMarketId": null,
    "refundModeActive": false,
    "resolvedAt": null,
    "members": [
      {
        "marketId": "0x...",
        "title": { "key": "market.X.title", "fallback": "Brazil wins?" },
        "yesPrice": "0.32",
        "volume": { "decimal": "45000", ... },
        "status": "active",
        "index": 0
      },
      {
        "marketId": "0x...",
        "title": { "key": "...", "fallback": "Argentina wins?" },
        "yesPrice": "0.28",
        ...
      }
    ],
    "capabilities": ["trade"]
  },
  "meta": { ... }
}
```

Cache: **hot (2s)**.

---

## Admin endpoints (SIWE + role)

### `POST /api/v2/admin/events`

Create event group metadata (Mongo overlay — linking markets đã tạo on-chain trước đó).

Body:

```json
{
  "eventId": "42",
  "name": "Ai thắng World Cup 2026?",
  "description": "...",
  "iconUrl": "...",
  "featured": true,
  "memberMarketIds": ["0x...", "0x...", "..."]
}
```

### `GET /api/v2/admin/events/{id}`

Admin view with full metadata + audit log.

### `PATCH /api/v2/admin/events/{id}`

Update name / description / featured.

### `GET /api/v2/admin/events/{id}/members`

List members.

### `POST /api/v2/admin/events/{id}/members`

Add market to event (update overlay; also must be linked on-chain).

### `DELETE /api/v2/admin/events/{id}/members/{marketId}`

Remove.

---

## Event vs Market filter

### List markets but NOT including event children

```
GET /api/v2/markets?status=active
```

Default: exclude `market.eventId != 0`.

### List members of one specific event

```
GET /api/v2/events/42   # preferred — full context
GET /api/v2/markets?eventId=42   # flat list only
```

### Include children in flat list

```
GET /api/v2/markets?includeEventChildren=true
```

Rare use case; dùng `/events/:id` nếu có thể.
