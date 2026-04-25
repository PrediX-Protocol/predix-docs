# Backend API

NestJS API v2 — view model cho FE/app. Wrap Indexer + overlay metadata từ MongoDB.

## Base URL

- Testnet: `https://cells-depends-prize-raising.trycloudflare.com` (cloudflared tunnel)
- Mainnet: TBD

## Khi nào dùng BE thay vì Indexer

| | Indexer (Ponder) | Backend (BE v2) |
|---|---|---|
| Raw on-chain data | ✅ | Wrap |
| Display metadata (title, category, icon) | ❌ | ✅ from MongoDB |
| Computed status + capabilities | ❌ | ✅ server-side |
| LocalizedString i18n key | ❌ | ✅ |
| Discriminator union market | ❌ | ✅ (binary/scalar/multi/sports/grouped) |
| Auth session (SIWE) | ❌ | ✅ |
| Admin actions | ❌ | ✅ |
| Cache (2s hot, 60s warm) | ❌ | ✅ |

Tóm lại: **FE / app user** dùng BE. **Bot / analytics** dùng Indexer.

## Response envelope

Success:
```json
{
  "data": <payload>,
  "meta": {
    "timestamp": 1740100000,
    "version": "v2",
    "reqId": "uuid-optional"
  }
}
```

Error:
```json
{
  "error": {
    "code": "MARKET_NOT_FOUND",
    "message": "Market ... not found",
    "details": [{ "path": "marketId", "message": "not a valid bytes32" }]
  },
  "meta": { "timestamp": 1740100000, "version": "v2" }
}
```

## Primitives

Wire format tuân thủ:

- **Address**: lowercase `0x[a-f0-9]{40}`, normalized ở boundary pipe.
- **MarketId**: lowercase `0x[a-f0-9]{64}`.
- **Price**: decimal string `"0.524"`, không JS `number`.
- **Money**: object `{ decimal: "10.5", raw: "10500000", decimals: 6, unit: "USDC" }`.
- **Timestamp**: unix seconds integer `1740100000`.
- **User string**: `{ key: "market.0xabc.title", fallback: "Will BTC..." }` — i18n key + fallback.
- **Color/icon**: `{ tokenKey: "semantic.positive" }` thay vì hex.

## Market discriminator

```ts
type Market = 
  | { kind: 'binary', binary: BinaryData, /* ... */ }
  | { kind: 'scalar', scalar: ScalarData, /* ... */ }
  | { kind: 'multi', multi: MultiData, /* ... */ }
  | { kind: 'sports', sports: SportsData, /* ... */ }
  | { kind: 'grouped', grouped: GroupedData, /* ... */ };
```

FE code: `market[market.kind]` — exhaustive switch, không flat field.

## Endpoints chính

### Markets & Events

```
GET  /api/v2/markets              list with filters + pagination
GET  /api/v2/markets/:id          single (id = hex bytes32)
GET  /api/v2/markets/:id/orderbook
GET  /api/v2/markets/:id/trades
GET  /api/v2/events
GET  /api/v2/events/:id           event detail với members
```

Query:
- `kind` — filter binary/scalar/multi/sports/grouped
- `status` — active/resolved/refunding (omit = all)
- `eventId` — trong event cụ thể
- `isFeatured` — boolean
- `categoryId`, `sortBy`, `limit`, `offset`

### Pricing

```
POST /api/v2/markets/:id/pricing/quote          quote trước swap
GET  /api/v2/markets/:id/pricing/view           combined CLOB + AMM view
POST /api/v2/markets-batch/price-views          batch up to 50
```

### User & Portfolio

```
GET  /api/v2/users/:address/orders?status=...  CLOB orders (no default filter)
GET  /api/v2/users/:address/portfolio
GET  /api/v2/users/:address/trades
GET  /api/v2/users/:address/pnl
GET  /api/v2/users/:address/profile
```

### Auth (SIWE)

```
GET  /api/v2/auth/challenge?address=0x...   → { message, nonce, expiresAt }
POST /api/v2/auth/verify                    → { sessionToken, user }
GET  /api/v2/auth/me                        [auth required]
PATCH /api/v2/auth/me                       [auth required]
POST /api/v2/auth/logout
```

### AA (Account Abstraction)

```
POST /api/v2/aa/auth/passkey/register/challenge
POST /api/v2/aa/auth/passkey/register/verify
POST /api/v2/aa/auth/passkey/login
POST /api/v2/aa/bundler                     Pimlico proxy
POST /api/v2/aa/paymaster/sponsor           sponsor UserOp
```

### Faucet

```
POST /api/v2/faucet                         claim test ETH + USDC
GET  /api/v2/faucet/cooldown?address=0x...  xem cooldown
```

### System

```
GET  /health                                mongo + indexer probe
GET  /api/v2/openapi.json                   OpenAPI 3.1 spec
GET  /api/v2/capabilities                   enum describe list
```

## Auth flow SIWE

1. User connect wallet.
2. `GET /api/v2/auth/challenge?address=0xabc` → nonce.
3. User ký message bằng wallet (viem `signMessage`).
4. `POST /api/v2/auth/verify` body `{ address, signature }`.
5. BE verify, set session cookie `predix_session` (HTTPOnly).
6. Request tiếp theo auto include cookie → `SessionAuthGuard` check.
7. Logout: `POST /api/v2/auth/logout` (idempotent).

Example:

```ts
// 1. Challenge
const chRes = await fetch(`${API}/auth/challenge?address=${addr}`);
const { message } = await chRes.json();

// 2. Sign
const signature = await walletClient.signMessage({ message });

// 3. Verify
const verifyRes = await fetch(`${API}/auth/verify`, {
  method: 'POST',
  credentials: 'include', // cookie
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ address: addr, signature }),
});
// session set via cookie
```

## Rate limit

- Public: 60 req/min/IP
- Authenticated: 300 req/min/user
- Auth endpoint (challenge, verify): 5 req/min (chống brute force)

## Cache

BE dùng 2 tier:
- **Hot** 2s — markets list, detail, orderbook, trades (data thay đổi nhanh).
- **Warm** 60s — user profile, category, stats (ít đổi).

Response có header `X-Cache: HIT | MISS` + `X-Cache-Tier: hot | warm`.

## OpenAPI generated types

BE publish OpenAPI 3.1 spec. FE generate types tự động:

```bash
# In FE/
npm run sync:schemas  # copy schemas from BE/src/schemas/v2/
npm run check:schemas-sync  # verify no drift
```

Types import:
```ts
import type { paths } from '@/types/api-v2';
import createClient from 'openapi-fetch';

const api = createClient<paths>({ baseUrl: API_URL });
const { data } = await api.GET('/markets/{id}', {
  params: { path: { id: '0x0001...' } },
});
```

## Forbidden packages (BE internal rule)

BE refuse các package sau (SPEC hard rule):
- `class-validator`, `class-transformer` (dùng zod + nestjs-zod)
- `axios` (dùng native `fetch` / `undici`)
- `node-cache` (dùng `lru-cache`)
- `ts-node` (dùng `tsx`)
- `ethers` (dùng `viem`)

## Error codes

Enum đóng (closed set):

| Code | HTTP | Ý nghĩa |
|---|---|---|
| `MARKET_NOT_FOUND` | 404 | Market id không tồn tại |
| `MARKET_PAUSED` | 400 | Market đang paused, không trade |
| `INDEXER_UNAVAILABLE` | 503 | Indexer circuit breaker tripped |
| `INVALID_ADDRESS` | 400 | Address không hợp lệ (non-hex, wrong length) |
| `INVALID_MARKET_ID` | 400 | MarketId không hợp lệ |
| `AUTH_REQUIRED` | 401 | Endpoint cần session |
| `AUTH_INVALID` | 401 | Session expired hoặc invalid |
| `FORBIDDEN` | 403 | Không đủ role |
| `VALIDATION_FAILED` | 400 | Request body không pass zod |
| `RATE_LIMIT_EXCEEDED` | 429 | Đã vượt rate limit |

Xem đủ danh sách: `GET /api/v2/capabilities`.

## Testing

BE có `mongodb-memory-server` + Fastify inject — integration test không cần external DB.

```bash
cd BE
pnpm test                    # all unit + integration
pnpm test:contract           # OpenAPI drift check
pnpm test -- markets.spec    # 1 file
```
