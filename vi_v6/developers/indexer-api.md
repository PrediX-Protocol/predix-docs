# Indexer API

REST API cung cấp market data, portfolio, price history. Ponder + PostgreSQL backend, Hono REST framework.

## Base URL

- **Mainnet**: `https://indexer.predix.app`
- **Testnet**: TBA — xem [Testnet info](testnet.md)

## Authentication

Public read-only. Không cần auth cho hầu hết endpoint.

Pro tier endpoint (high rate limit) yêu cầu API key — xem [Bots & API](bots-api.md).

## Envelope format

Mọi response thành công:

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

Single item (không phải list): `"meta": null`.

Error:
```json
{
  "error": "MarketNotFound",
  "message": "Market 0xabc... not found"
}
```

## Endpoints

### Markets

#### `GET /api/markets` — list

Query:
- `status` — `active | resolved | refunding` (omit = all)
- `limit` — max 100 (default 20)
- `offset` — pagination
- `sort` — `newest | volume | ending_soon`
- `category` — `crypto | sports | politics | ...`
- `featured` — boolean

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

#### `GET /api/markets/:id` — single

`id` = decimal string. Response `data` = market object hoặc `null`.

#### `GET /api/markets/:id/trades` — trade history

Union Router trades + CLOB taker fills. Query: `limit`, `offset`.

#### `GET /api/markets/:id/positions` — position per user

Query: `address` (optional filter), `limit`, `offset`.

#### `GET /api/markets/:id/orders` — CLOB orders

Query: `status` (OPEN/PENDING/FILLED/CANCELLED), `side`, `limit`.

#### `GET /api/markets/:id/orderbook` — current order book

Snapshot bid/ask grouped by price tick.

#### `GET /api/markets/:id/holders` — token holders

Top holders YES + NO of market.

### Pricing

#### `GET /api/markets/:id/amm-state` — Uniswap v4 pool state

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

#### `GET /api/markets/:id/price-delta?window=24` — price change

Window 1-720 giờ.

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
  }
}
```

#### `GET /api/markets/:id/volume-window?window=24` — volume window

Default 0 nếu không có trade.

#### `GET /api/markets/:id/candles?timeframe=1h&from=...&to=...` — OHLC chart

Timeframes: `1m`, `5m`, `15m`, `1h`, `4h`, `1d`, `1w`.

```json
{
  "data": [
    { "ts": 1740000000, "open": "0.50", "high": "0.55", "low": "0.49", "close": "0.52", "volume": "12000.0" },
    ...
  ]
}
```

### Events (multi-outcome)

#### `GET /api/events` — list events

#### `GET /api/events/:id` — detail với members

```json
{
  "data": {
    "id": "42",
    "name": "FIFA WC 2026 Winner",
    "endTime": 1785859200,
    "marketIds": ["1", "2", "3", ...],
    "isResolved": false,
    "winningIndex": null
  }
}
```

### Portfolio

#### `GET /api/users/:address/portfolio?includeResolved=true`

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

#### `GET /api/users/:address/stats` — aggregated

```json
{
  "data": {
    "address": "0xabc...",
    "totalVolume": "5280.000000",
    "tradeCount": 42,
    "totalRealizedPnl": "+320.500000",
    "resolvedPositions": 18,
    "wonPositions": 11,
    "accuracyScoreSum": "820000",
    "accuracyScoreCount": 18,
    "lastTradeAt": 1740100000
  }
}
```

#### `GET /api/users` — leaderboard

Query: `sort` (`pnl | volume | accuracy`), `period`, `limit`, `offset`.

#### `GET /api/users/:address/lp-positions` — LP NFTs

#### `GET /api/users/:address/badges` — earned badges

### Protocol stats

#### `GET /api/stats` — singleton

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

#### `GET /api/health` — indexer health

```json
{
  "status": "ok",
  "latestIndexedBlock": 50123456,
  "lagBlocks": 3
}
```

#### `GET /api/doc` — OpenAPI spec JSON
#### `GET /api/docs` — Swagger UI

## BigInt serialization

Mọi amount on-chain là `uint256` → serialize thành **decimal string** ở boundary:

- `"125000.000000"` thay vì number `125000.000001` (precision loss).
- Parse client-side: `BigInt(stripDecimal(str))`.

## Indexer lag

- **L2 finality**: ~12-15 min trên Unichain.
- **Indexer lag** từ head: thường < 60s (measure qua `/api/health`).
- Data vừa trade chưa thấy → wait 10-30s, retry.

## Reorg safety

Ponder handle reorg automatic — khi chain reorg, indexer revert + replay. Client không cần custom logic.

## Examples

### Fetch top 10 market by volume

```typescript
const res = await fetch('https://indexer.predix.app/api/markets?sort=volume&limit=10');
const { data: markets } = await res.json();
markets.forEach(m => console.log(`${m.question} — $${m.volume}`));
```

### Monitor price change

```typescript
setInterval(async () => {
  const res = await fetch(`https://indexer.predix.app/api/markets/${id}/price-delta?window=24`);
  const { data } = await res.json();
  if (data.percentBps > 500) { // 5%+ trong 24h
    alert('Big move');
  }
}, 60_000);
```

### Subscribe realtime via WebSocket

```typescript
const ws = new WebSocket('wss://indexer.predix.app/ws');
ws.send(JSON.stringify({
  subscribe: ['market:0x...:trades', 'market:0x...:price'],
}));
ws.onmessage = (e) => {
  const msg = JSON.parse(e.data);
  console.log(msg);
};
```

## Limits

| Tier | Rate limit | Quota |
|---|---|---|
| Free (no API key) | 60 req/min/IP | 10,000 req/day |
| Pro ($20/month) | 600 req/min | 1M req/day |
| Enterprise | Custom | Custom |

WebSocket: 10 connections/IP, unlimited messages.

API key request: [bots-api.md](bots-api.md).
