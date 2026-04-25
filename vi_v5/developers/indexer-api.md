# Indexer API

REST API cung cấp market data, portfolio, price history. Ponder 0.16 + Hono framework.

## Base URL

- Testnet: (internal, không public)
- Mainnet: TBD

## Authentication

Không cần. Public read-only.

## Envelope

Mọi response:

```json
{
  "data": <payload>,
  "meta": {
    "limit": 20,
    "offset": 0,
    "count": 150
  }
}
```

Single item (not list): `"meta": null`.

Error (HTTP 4xx/5xx):
```json
{
  "error": "MarketNotFound",
  "message": "Market 0xabc... not found"
}
```

## Endpoints

### Markets

**`GET /api/markets`** — list

Query:
- `status` — `active | resolved | refunding` (optional; omit = all)
- `limit` — max 100 (default 20)
- `offset` — pagination
- `sort` — `newest | volume | ending_soon`

Response:
```json
{
  "data": [
    {
      "id": "1",
      "marketIdHex": "0x000...01",
      "question": "BTC > $100k trước 2027?",
      "endTime": 1798752000,
      "yesPrice": "0.62",
      "totalCollateral": "125000.000000",
      "volume": "523000.000000",
      "tradeCount": 2341,
      "isResolved": false,
      "outcome": null,
      "creator": "0xfad5...",
      "oracle": "0x7887...",
      "createdAt": 1740000000,
      "createdBlock": 49800000
    }
  ],
  "meta": { "limit": 20, "offset": 0, "count": 150 }
}
```

**`GET /api/markets/:id`** — single

`id` = decimal string. Response `data` = single market object hoặc `null`.

**`GET /api/markets/:id/trades`** — trade history

Union Router trades + CLOB taker fills. Query: `limit`, `offset`.

**`GET /api/markets/:id/positions`** — position per user

Query: `address` (optional filter), `limit`, `offset`.

**`GET /api/markets/:id/orders`** — CLOB orders

Query: `status` (OPEN/PENDING/FILLED/CANCELLED), `side`, `limit`.

**`GET /api/markets/:id/orderbook`** — current order book

Snapshot bid/ask grouped by price tick.

### Pricing

**`GET /api/markets/:id/amm-state`** — Uniswap v4 pool state

```json
{
  "data": {
    "poolId": "0x...",
    "marketId": "...",
    "yesIsCurrency0": true,
    "sqrtPriceX96": "79228162514264...",
    "liquidity": "1000000000",
    "tick": -23000,
    "updatedAt": 1740100000,
    "updatedBlock": 49850000
  },
  "meta": null
}
```

**`GET /api/markets/:id/price-delta?window=24`** — price change

Window trong khoảng 1–720 giờ (max 30 ngày).

```json
{
  "data": {
    "currentYesPrice": "0.62",
    "previousYesPrice": "0.55",
    "absolute": "0.07",
    "percentBps": 1272,
    "windowHours": 24,
    "latestAt": 1740100000,
    "baselineAt": 1740013600
  },
  "meta": null
}
```

**`GET /api/markets/:id/volume-window?window=24`** — volume trong window

Never null — default 0 nếu không có trade.

### Events (multi-outcome)

**`GET /api/events`** — list events

**`GET /api/events/:id`** — detail với members

```json
{
  "data": {
    "id": "42",
    "name": "FIFA WC 2026 Winner",
    "endTime": 1785859200,
    "marketIds": ["1", "2", "3", ...],
    "isResolved": false,
    "winningIndex": null
  },
  "meta": null
}
```

### Portfolio

**`GET /api/users/:address/portfolio?includeResolved=true`**

```json
{
  "data": [
    {
      "marketId": "1",
      "user": "0xabc...",
      "yesBalance": "205.300000",
      "noBalance": "0",
      "totalSpent": "99.200000",
      "totalReceived": "0",
      "realizedPnl": "0",
      "outcomeAtResolve": null
    }
  ]
}
```

**`GET /api/users/:address/stats`** — aggregated

```json
{
  "data": {
    "address": "0xabc...",
    "totalVolume": "5280.000000",
    "tradeCount": 42,
    "totalRealizedPnl": "+320.500000",
    "resolvedPositions": 18,
    "wonPositions": 11,
    "accuracyScoreSum": "820000",  // micro units
    "accuracyScoreCount": 18,
    "lastTradeAt": 1740100000
  }
}
```

**`GET /api/users`** — leaderboard

Query: `sort` (`pnl | volume | accuracy`), `limit`, `offset`.

### Protocol stats

**`GET /api/stats`** — singleton

```json
{
  "data": {
    "totalMarkets": 152,
    "activeMarkets": 98,
    "resolvedMarkets": 54,
    "totalVolume": "12500000.000000",
    "totalTrades": 48250,
    "totalUsers": 3521,
    "totalCollateral": "2100000.000000",
    "totalFeesCollected": "42000.000000"
  }
}
```

### System

**`GET /api/health`** — indexer health

```json
{
  "status": "ok",
  "latestIndexedBlock": 50123456,
  "lagBlocks": 3
}
```

**`GET /api/doc`** — OpenAPI spec JSON.

**`GET /api/docs`** — Swagger UI browser.

## BigInt serialization

Mọi amount on-chain là `uint256` → serialize thành **decimal string** ở boundary:

- `125000.000000` thay vì number `125000.000001` (precision loss).
- Parse bằng `BigInt(str)` client-side.

## Indexer lag

- **Finality window**: ~12–15 min trên L2 Unichain.
- **Indexer lag** từ head block: thường < 60s (measure qua `/api/health`).
- Nếu data vừa trade mà chưa thấy → chờ 10–30s, retry.

## Reorg safety

Ponder handle reorg — khi chain reorg, indexer revert + replay. BE không được cache data trước finality.

## Chain block numbers

- `createdBlock`, `updatedBlock` — block mà state set.
- `DEPLOY_BLOCK = 49799033` — START_BLOCK for indexer = 49799032.

## Examples

### Fetch top 10 market by volume

```ts
const res = await fetch('https://indexer.testnet.predix.app/api/markets?sort=volume&limit=10');
const { data: markets } = await res.json();
markets.forEach(m => console.log(`${m.question} — $${m.volume}`));
```

### Monitor price change

```ts
const window = 24;
setInterval(async () => {
  const res = await fetch(`https://indexer.../api/markets/${id}/price-delta?window=${window}`);
  const { data } = await res.json();
  if (data.percentBps > 500) { // 5%+ trong 24h
    alert('Big move');
  }
}, 60_000);
```

## Limits

- Public: **60 req/min/IP**, 10.000 req/day/IP.
- Larger quota: contact.
