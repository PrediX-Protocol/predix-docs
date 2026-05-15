# API reference

PrediX exposes 3 layers for developers:

| Layer                                        | Purpose                                                          | When to use                        |
| -------------------------------------------- | ---------------------------------------------------------------- | ---------------------------------- |
| **Indexer API** (Ponder + PostgreSQL + Hono) | Raw on-chain data: market, position, trade, OHLC                 | Bots, analytics, raw data          |
| **Backend API** (NestJS v2)                  | View model for FE/app: cache + metadata + i18n + auth + comments | FE / mobile app user-facing        |
| **Smart contract events**                    | Canonical source of truth on-chain                               | Custom subgraph, monitor, listener |

When to use BE vs Indexer:

| Need                                     | Indexer | Backend       |
| ---------------------------------------- | ------- | ------------- |
| Raw on-chain data                        | Yes     | wraps Indexer |
| Display metadata (title, category, icon) | No      | Yes (MongoDB) |
| Localized i18n                           | No      | Yes           |
| Auth session (SIWE)                      | No      | Yes           |
| Cache (2s hot / 60s warm)                | No      | Yes           |
| Notifications / comments                 | No      | Yes           |

Bot/analytics raw data -> Indexer. FE/app user-facing -> Backend.

## Base URL

| Env                | Indexer                                | Backend                                |
| ------------------ | -------------------------------------- | -------------------------------------- |
| **Testnet** (live) | Gated — see [Testnet info](testnet.md) | Gated — see [Testnet info](testnet.md) |
| **Mainnet** (TBA)  | `https://indexer.predix.app`           | `https://api.predix.app`               |

Schema is identical across both environments — switching testnet to mainnet only requires changing the base URL.

## Authentication

* **Indexer**: public read-only.
* **Backend**: public + auth via SIWE cookie session (details in the SIWE auth flow section below).

## Response envelope

Indexer success:

```json
{
  "data": <payload>,
  "meta": { "limit": 20, "offset": 0, "count": 150 }
}
```

Backend success:

```json
{
  "data": <payload>,
  "meta": { "timestamp": 1740100000, "version": "v2", "reqId": "uuid-optional" }
}
```

Single item: `"meta": null` (Indexer) or meta.list fields omitted (BE).

Error (both):

```json
{
  "error": "MarketNotFound",
  "message": "Market 0xabc... not found"
}
```

BE adds `details: [{path, message}]` for validation errors + a `code` enum (see Error codes section).

***

## Indexer endpoints

### Markets

```
GET /api/markets                       list (status, limit, offset, sort, category, featured)
GET /api/markets/:id                   single (id = decimal string)
GET /api/markets/:id/trades            Union Router trades + CLOB taker fills
GET /api/markets/:id/positions         per-user positions (filter by address)
GET /api/markets/:id/orders            CLOB orders (status, side)
GET /api/markets/:id/orderbook         current snapshot grouped by tick
GET /api/markets/:id/holders           top YES + NO holders
```

Markets list response:

```json
{
  "data": [{
    "id": "1",
    "marketIdHex": "0x000...01",
    "question": "BTC > $100k before 2027?",
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
  }],
  "meta": { "limit": 20, "offset": 0, "count": 150 }
}
```

### Pricing

```
GET /api/markets/:id/amm-state                    Uniswap v4 pool state
GET /api/markets/:id/price-delta?window=24        price change (1-720h window)
GET /api/markets/:id/volume-window?window=24      volume window
GET /api/markets/:id/candles?timeframe=1h&from=&to=  OHLC
```

Timeframes: `1m`, `5m`, `15m`, `1h`, `4h`, `1d`, `1w`.

### Events (multi-outcome)

```
GET /api/events
GET /api/events/:id              detail with marketIds + winningIndex
```

### Portfolio

```
GET /api/users/:address/portfolio?includeResolved=true
GET /api/users/:address/stats              aggregated PnL, accuracy, volume
GET /api/users                             leaderboard (sort=pnl|volume|accuracy)
```

### Protocol stats

```
GET /api/stats                  totalMarkets, totalVolume, totalTrades, totalUsers, etc
```

### System

```
GET /api/health                 latestIndexedBlock, lagBlocks
GET /api/doc                    OpenAPI JSON
GET /api/docs                   Swagger UI
```

***

## Backend endpoints (v2)

### Primitives — strict wire format

| Type        | Format                     | Example                                                     |
| ----------- | -------------------------- | ----------------------------------------------------------- |
| Address     | lowercase `0x[a-f0-9]{40}` | `"0xfad5..."`                                               |
| MarketId    | lowercase `0x[a-f0-9]{64}` | `"0xabc...64hex"`                                           |
| Price       | decimal string             | `"0.524"`                                                   |
| Money       | object                     | `{decimal:"10.5", raw:"10500000", decimals:6, unit:"USDC"}` |
| Timestamp   | unix seconds int           | `1740100000`                                                |
| User string | object                     | `{key:"market.0xabc.title", fallback:"Will BTC..."}`        |

### Market discriminator (Stripe pattern)

```typescript
type Market =
  | { kind: 'binary', binary: BinaryData, /* ... */ }
  | { kind: 'scalar', scalar: ScalarData, /* ... */ }
  | { kind: 'multi', multi: MultiData, /* ... */ }
  | { kind: 'sports', sports: SportsData, /* ... */ }
  | { kind: 'grouped', grouped: GroupedData, /* ... */ };
```

FE: `market[market.kind]` — exhaustive switch.

### Markets & events

```
GET  /api/v1/markets                  list with filters + pagination
GET  /api/v1/markets/:id              single (id = hex bytes32)
GET  /api/v1/markets/:id/orderbook
GET  /api/v1/markets/:id/trades
GET  /api/v1/markets/:id/holders
GET  /api/v1/markets/:id/comments
GET  /api/v1/events
GET  /api/v1/events/:id
```

### Pricing

```
POST /api/v1/markets/:id/pricing/quote     quote before swap
GET  /api/v1/markets/:id/pricing/view      combined CLOB + AMM view
POST /api/v1/markets-batch/price-views     batch up to 50
GET  /api/v1/markets/:id/candles           OHLC
```

### User & portfolio

```
GET  /api/v1/users/:address/orders
GET  /api/v1/users/:address/portfolio
GET  /api/v1/users/:address/trades
GET  /api/v1/users/:address/pnl
GET  /api/v1/users/:address/profile
GET  /api/v1/users/:address/calibration
GET  /api/v1/users/:address/follows
GET  /api/v1/users/:address/followers
```

### Auth (SIWE)

```
GET  /api/v1/auth/challenge?address=0x...
POST /api/v1/auth/verify
GET  /api/v1/auth/me            [auth required]
PATCH /api/v1/auth/me           [auth required]
POST /api/v1/auth/logout
```

### Account abstraction

```
POST /api/v1/aa/auth/passkey/register/challenge
POST /api/v1/aa/auth/passkey/register/verify
POST /api/v1/aa/auth/passkey/login
POST /api/v1/aa/bundler                 Pimlico bundler proxy
POST /api/v1/aa/paymaster/sponsor       sponsor UserOp
```

### Notifications

```
GET    /api/v1/notifications?unread=true
PATCH  /api/v1/notifications/:id/read
POST   /api/v1/notifications/read-all
```

### Rewards & gamification

```
GET  /api/v1/rewards/boxes
GET  /api/v1/rewards/challenges
GET  /api/v1/users/:address/achievements
GET  /api/v1/leaderboard
```

### Comments & social

```
GET  /api/v1/markets/:id/comments?sort=top|new&limit=50
POST /api/v1/markets/:id/comments        [auth]
```

### Governance

> **Planned**: Governance endpoints for vePRX voting will be available in Phase 2.

### System

```
GET /health                               mongo + indexer probe
GET /docs-json                            OpenAPI spec (JSON)
```

### SIWE auth flow

![SIWE auth: GET /auth/challenge -> server returns nonce -> user signMessage -> POST /auth/verify -> BE verifies ECDSA -> set HTTPOnly cookie 7 days](../.gitbook/assets/14-siwe-auth.svg)

```typescript
// 1. Challenge
const { message } = await fetch(`${API}/api/v1/auth/challenge?address=${addr}`).then(r => r.json());

// 2. Sign
const signature = await walletClient.signMessage({ message });

// 3. Verify
await fetch(`${API}/api/v1/auth/verify`, {
  method: 'POST',
  credentials: 'include',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ address: addr, signature }),
});
```

### Cache headers

BE has 2 tiers: **hot 2s** (markets list/detail, orderbook, trades) - **warm 60s** (user profile, category, stats).

Response: `X-Cache: HIT | MISS`, `X-Cache-Tier: hot | warm`.

### Error codes (closed set)

| Code                   | HTTP | Meaning                         |
| ---------------------- | ---- | ------------------------------- |
| `MARKET_NOT_FOUND`     | 404  | Market id does not exist        |
| `MARKET_PAUSED`        | 400  | Market is currently paused      |
| `INDEXER_UNAVAILABLE`  | 503  | Indexer circuit breaker tripped |
| `INVALID_ADDRESS`      | 400  | Address malformed               |
| `INVALID_MARKET_ID`    | 400  | MarketId malformed              |
| `AUTH_REQUIRED`        | 401  | Endpoint requires a session     |
| `AUTH_INVALID`         | 401  | Session expired or invalid      |
| `FORBIDDEN`            | 403  | Insufficient role               |
| `VALIDATION_FAILED`    | 400  | Request body failed validation  |
| `RATE_LIMIT_EXCEEDED`  | 429  | Rate limit exceeded             |
| `INSUFFICIENT_BALANCE` | 400  | Wallet has insufficient balance |
| `SLIPPAGE_EXCEEDED`    | 400  | On-chain revert due to slippage |

### OpenAPI typed client

BE publishes OpenAPI 3.1. FE generates types automatically:

```bash
npm run sync:schemas
npm run check:schemas-sync
```

```typescript
import type { paths } from '@predix/api-types';
import createClient from 'openapi-fetch';

const api = createClient<paths>({ baseUrl: 'https://api.predix.app' });
const { data } = await api.GET('/markets/{id}', {
  params: { path: { id: '0x0001...' } },
});
```

***

## Smart contract events

Source of truth on-chain — bot listeners, custom subgraphs, and monitoring services should consume from here.

![Event source: Router.Trade = canonical (volume + trades count), Hook\_MarketTraded = analytics only (priceSnapshot, NOT volume), PositionSplit/Merge/Redeem/Refund = audit rows always land](../.gitbook/assets/63-event-source-truth.svg)

* **Canonical trade**: `Router.Trade` — `protocolStats.totalVolume / totalTrades` **only** increments from this event.
* **AMM swap analytics**: `Hook_MarketTraded` — priceSnapshot only, does not count volume (avoids double-counting).
* **Audit rows always land**: `PositionSplit`, `PositionMerged`, `TokensRedeemed`, `MarketRefunded` — recorded regardless of recipient.

### Router events

```solidity
event Trade(uint256 indexed marketId, address indexed trader, address indexed recipient, TradeType tradeType, uint256 amountIn, uint256 amountOut, uint256 clobFilled, uint256 ammFilled);
event DustRefunded(address indexed recipient, address indexed token, uint256 amount);
event ClobSkipped(uint256 indexed marketId, address indexed recipient, bytes4 reason);
```

`Trade` -> tables: `trade`, `position` (if recipient is not a protocol contract), `market.volume`, `market.tradeCount`, `priceSnapshot` (source="router"), `protocolStats`, `user`, `userStats`.

### Exchange events

```solidity
event OrderPlaced(bytes32 indexed orderId, uint256 indexed marketId, address indexed owner, Side side, uint256 price, uint256 amount, bytes32 builder);
event OrderMatched(bytes32 indexed makerOrderId, bytes32 indexed takerOrderId, uint256 indexed marketId, MatchType matchType, uint256 amount, uint256 price, bytes32 makerBuilder, bytes32 takerBuilder);
event OrderCancelled(bytes32 indexed orderId);
event TakerFilled(uint256 indexed marketId, address indexed taker, address indexed recipient, Side takerSide, uint256 totalFilled, uint256 totalCost, uint256 matchCount);
event FeeCollected(uint256 indexed marketId, uint256 amount);
```

Tables: `exchangeOrder`, `orderMatch`, `takerFill`, `position` (non-protocol).

### MarketFacet events

```solidity
event MarketCreated(uint256 indexed marketId, address indexed creator, address indexed oracle, address yesToken, address noToken, uint256 endTime, string question);
event PositionSplit(uint256 indexed marketId, address indexed user, uint256 amount);
event PositionMerged(uint256 indexed marketId, address indexed user, uint256 amount);
event MarketResolved(uint256 indexed marketId, bool outcome, address indexed resolver);
event MarketEmergencyResolved(uint256 indexed marketId, bool outcome, address indexed resolver);
event TokensRedeemed(uint256 indexed marketId, address indexed user, uint256 winningBurned, uint256 losingBurned, uint256 fee, uint256 payout);
event RefundModeEnabled(uint256 indexed marketId, address indexed enabler);
event MarketRefunded(uint256 indexed marketId, address indexed user, uint256 yesBurned, uint256 noBurned, uint256 payout);
```

Tables: `market`, `outcomeToken`, `positionSplit`, `positionMerge`, `redemption`, `refundClaim`, `feeConfigChange`.

### EventFacet events

```solidity
event EventCreated(uint256 indexed eventId, address indexed creator, uint256 endTime, string name, uint256[] marketIds, address oracle);
event EventResolved(uint256 indexed eventId, address indexed resolver);
event EventRefundModeEnabled(uint256 indexed eventId);
```

Table: `eventGroup`.

### Hook events

```solidity
event Hook_PoolRegistered(uint256 indexed marketId, PoolId indexed poolId, address yesToken, address quoteToken, bool yesIsCurrency0);
event Hook_MarketTraded(uint256 indexed marketId, address indexed trader, bool isBuy, uint256 usdcVolume, uint256 yesVolume, uint256 yesPrice, uint256 noPrice);
```

`Hook_PoolRegistered` -> `hookPoolBinding` (essential lookup for filtering PoolManager events). `Hook_MarketTraded` -> `priceSnapshot` (source="hook\_amm"), `market.lastTradeAt`. **Does NOT** count volume.

### Oracle events

```solidity
// ManualOracle
event OracleReportCreated(bytes32 indexed marketId, bool outcome, address reporter);
event OracleReportRevoked(bytes32 indexed marketId, address operator);

// ChainlinkOracle
event OracleMarketRegistered(bytes32 indexed marketId, address feed, uint256 threshold, bool gte, uint256 snapshotAt);
event OracleMarketResolved(bytes32 indexed marketId, bool outcome);
```

### Diamond admin events

```solidity
event RoleGranted(bytes32 indexed role, address indexed account, address sender);
event RoleRevoked(bytes32 indexed role, address indexed account, address sender);
event DiamondCut(FacetCut[] cuts, address init, bytes data);
event GlobalPaused(address account);
event ModulePaused(bytes32 module, address account);
```

### OutcomeToken (ERC-20)

```solidity
event Transfer(address indexed from, address indexed to, uint256 value);
event Approval(address indexed owner, address indexed spender, uint256 value);
```

Each market has 1 YES + 1 NO token. Table: `holder` — canonical balance view.

### PoolManager (Uniswap v4)

Filter by `hookPoolBinding` membership first:

* `Initialize` -> insert `ammPoolState`
* `Swap` -> update `sqrtPriceX96`, `liquidity`, `tick`
* `ModifyLiquidity` -> update `liquidity += delta`

***

## WebSocket

For real-time data, see [WebSocket](websocket.md).

## Common patterns

### Fetch top 10 markets by volume

```typescript
const res = await fetch('https://indexer.predix.app/api/markets?sort=volume&limit=10');
const { data: markets } = await res.json();
markets.forEach(m => console.log(`${m.question} — $${m.volume}`));
```

### Listen to `Trade` events in realtime

```typescript
import { createPublicClient, http } from 'viem';
import { unichainSepolia } from 'viem/chains';

const client = createPublicClient({ chain: unichainSepolia, transport: http() });

client.watchContractEvent({
  address: ROUTER,
  abi: routerAbi,
  eventName: 'Trade',
  args: { marketId },
  onLogs: (logs) => logs.forEach(log => console.log(log.args)),
});
```

## Limits

| Tier   | Public        | Auth             | Quota          |
| ------ | ------------- | ---------------- | -------------- |
| Free   | 60 req/min/IP | 300 req/min/user | 10,000 req/day |

Auth endpoint (challenge/verify): 5/min.

## BigInt serialization

All on-chain amounts are `uint256` -> serialized as **decimal strings** at the boundary (`"125000.000000"` instead of a number, to avoid precision loss). Parse client-side: `BigInt(stripDecimal(str))`.

## Indexer lag

* **L2 finality**: \~12-15 min on Unichain.
* **Indexer lag** from head: typically < 60s (`/api/health`).
* Just traded but don't see it -> wait 10-30s, retry.

Ponder handles reorgs automatically — chain reorg -> indexer reverts + replays. Clients need no custom logic.
