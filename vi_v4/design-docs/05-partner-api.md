---
description: Partner & integration API — dedicated tier cho market makers, aggregators, data providers. Beyond public /api/v2.
---

# Partner & Integration API

**Document status**: v1.0 — 2026-04 (design spec, rollout Q2 2026 post-mainnet)
**Audience**: Market makers, aggregators (1inch, LI.FI, Jumper), data providers (Zapper, DeBank), analytics platforms, institutional traders
**Contact**: `partnerships@predixpro.io`

---

## 1. Overview

Public `/api/v2/*` serves FE + retail users with rate limits suitable for typical app usage. Partners have different needs:

- **Market makers**: need real-time orderbook push, low-latency fills, high request volume
- **Aggregators**: need deterministic quotes + atomic swap integration
- **Data platforms**: need bulk historical + portfolio queries
- **Institutional traders**: need elevated limits + SLA guarantees

Partner API = **superset of public API** with elevated tier + dedicated features. Same contracts, same data model, different access levels.

## 2. Partner tiers

| Tier | Target | Rate limit | Features | Cost |
|---|---|---|---|---|
| **Public** (default) | Retail, small dApp | 1000 req/min/IP | REST only | Free |
| **Developer** | Indie builder, bot | 5000 req/min/key | REST + session WS | Free (API key required) |
| **Partner** | MM, aggregator, data platform | 50K req/min/key | REST + push WS + webhooks + custom endpoints | Revenue share or monthly fee (TBD) |
| **Institutional** | Large institutional trader, market maker prop desk | Custom | SLA contract + dedicated support + co-location discussion | Custom contract |

Tier upgrade: request via `partnerships@predixpro.io`, undergo short KYB review (Know Your Business), receive API key + onboarding.

## 3. Partner API key

### 3.1 Issuance

```
POST /api/v2/auth/request-partner-access  (authenticated with SIWE)

Body:
{
  "organization": "Acme Capital",
  "useCase": "market making on YES/NO outcome tokens",
  "projectedVolume": "100K USDC / day",
  "website": "https://acme.capital"
}

Response (after manual approval):
{
  "apiKey": "predix_live_pk_...",
  "tier": "partner",
  "rateLimit": { "reqPerMin": 50000, "burstSize": 1000 },
  "features": ["realtime_ws", "webhooks", "custom_endpoints", "historical_bulk"]
}
```

Auth header: `Authorization: Bearer <api_key>` or `X-API-Key: <api_key>`.

### 3.2 Key management

- Partner can rotate key anytime via admin panel
- Multiple keys per organization (e.g., prod + staging) allowed
- Keys scoped: read-only vs. read-write (read-write for user-action on behalf of signed-in user, rare)
- Revocation: immediate via admin panel

## 4. Features per tier

### 4.1 Public (default)

All `/api/v2/*` endpoints listed in [Backend API Overview](../backend-api/00-overview.md).

### 4.2 Developer (with API key)

Everything in Public +:

- **Dedicated API key**: don't share IP rate limit with other users
- **Higher limits**: 5K req/min vs. 1K
- **WebSocket session** (Phase 2): single-user push for own portfolio / orders (auth required)
- **Extended cache bypass**: force-refresh endpoints (for testing)

Rate limit tier enforced at BE level via key lookup.

### 4.3 Partner (business agreement)

Everything in Developer +:

- **Real-time push WebSocket** (global events, not just own account):
  - Order placements + fills (all markets or filtered)
  - Price snapshots per market
  - Market lifecycle events (created, resolved, refunded)
- **Webhook delivery** (server-push HTTP POST to partner URL):
  - Configurable event filter
  - Retry with exponential backoff (up to 24h)
  - Signed with shared HMAC secret
- **Bulk historical endpoints**: query wide time ranges (BE: currently limited to 7d per request)
- **Custom endpoints**: partner-specific views if needed
- **Priority support**: Slack channel + quicker response time
- **SLA**: 99.9% uptime commitment

### 4.4 Institutional

Custom SLA contract negotiated per partner:

- Dedicated infrastructure (isolated BE instance)
- Custom caching strategy
- Co-location options near exchange infrastructure (for MM)
- Custom webhook retry policy
- Priority incident response (paged within 5 min)
- Legal contract with financial SLA guarantees

## 5. Partner-specific endpoints

### 5.1 Real-time WebSocket

```
wss://partner-ws.predixpro.io/v2?apiKey=<key>

Subscribe:
{ "action": "subscribe", "channels": ["trades", "orderbook:*", "markets:resolved"] }

Events:
{
  "channel": "trades",
  "data": {
    "marketId": "42",
    "trader": "0xabc...",
    "tradeType": "BUY_YES",
    "amountIn": { "decimal": "100", ... },
    "amountOut": { "decimal": "192.3", ... },
    "yesPrice": "0.52",
    "timestamp": 1776089500
  }
}
```

Channels:
- `trades` — all trades (via Router canonical Trade event)
- `trades:market:<id>` — filtered to one market
- `trades:user:<addr>` — filtered to one user
- `orderbook:<id>` — orderbook delta for market
- `orders:user:<addr>` — user's order fills
- `markets:resolved` — market resolution events
- `markets:created` — new markets

### 5.2 Webhooks

Configure in partner admin panel:

```
POST /api/v2/partner/webhooks

Body:
{
  "url": "https://acme.capital/predix/webhook",
  "events": ["trades", "markets.resolved", "orders.filled"],
  "filter": { "markets": ["42", "43"] },
  "secret": "<shared HMAC secret>"
}
```

PrediX POSTs event to URL:

```
POST <partner_url>
Headers:
  X-Predix-Signature: hmac_sha256(<secret>, body)
  X-Predix-Event: trade
  X-Predix-Id: <event_id>
  Content-Type: application/json

Body:
{
  "event": "trade",
  "data": { ... trade object ... },
  "timestamp": 1776089500,
  "id": "evt_abc123"
}
```

Partner responds with 200 for acknowledgment. Retry on non-2xx:

- Immediate retry
- 30s retry
- 5min retry
- 1h retry
- 6h retry
- 24h retry — after this, discarded

Use `X-Predix-Id` for idempotency (dedup at partner side).

### 5.3 Bulk historical data

```
GET /api/v2/partner/history/trades?
  since=1776000000&
  until=1776086400&
  limit=10000&
  cursor=<opaque>
```

- Limit: up to 10K trades per call (vs. 100 public)
- Keyset-based pagination for large ranges
- Supports compression (`Accept-Encoding: gzip`)
- Export format: JSON or CSV via `Accept` header

```
GET /api/v2/partner/history/orders?...
GET /api/v2/partner/history/resolutions?...
```

### 5.4 Custom endpoints (per contract)

Some partners request dedicated endpoints:

- **Aggregator quote cache**: pre-computed route quotes refreshed periodically
- **MM market depth feed**: aggregated depth across multiple markets
- **Analytics exposure**: PnL attribution data with aggregated cohorts

These are partner-specific + negotiated. Contact partnerships team.

## 6. Partner-specific integration patterns

### 6.1 Market maker integration

**Goal**: Post + cancel limit orders + monitor fills in real-time.

**Pattern:**

1. **Setup**
   - Request Partner tier API key
   - Configure WebSocket subscription for own fills
   - Optional: webhook for high-value markets
   
2. **Strategy execution** (bot running 24/7)
   - Split USDC → inventory
   - Compute fair value + post bid/ask spread
   - Monitor `orders:user:<bot_address>` WS for fills
   - Reposition based on fills + inventory skew
   
3. **Risk mgmt**
   - Kill switch on adverse fill pattern
   - Real-time inventory tracking (per-market YES/NO balance)
   - Max position cap per market (ballooning check)

**Recommended infrastructure:**

- VPS near Singapore data center (1-5ms latency to BE)
- Dedicated RPC (paid Alchemy tier or Unichain private RPC)
- Redis for state tracking
- Monitoring with Prometheus

**Sample code repo**: `github.com/predix-protocol/examples/mm-starter` (Q3 2026)

### 6.2 Aggregator integration (1inch, LI.FI style)

**Goal**: Include PrediX in aggregator's route list.

**Pattern:**

1. **Setup**
   - Request Partner tier
   - Integrate `quoteBuyYes` + `quoteSellYes` into aggregator's quote fetcher
   - Cache quotes 2-5s aligned with BE hot cache

2. **Execution flow**
   - User requests aggregator swap USDC → YES for marketId
   - Aggregator quotes own DEX list + PrediX Router
   - PrediX returns best execution price via Router
   - Aggregator executes via Router's buyYes
   - `_refundAndAssertZero` invariant handles any refund

3. **Certification**
   - Partner fills integration checklist
   - PrediX team reviews + issues certification
   - Listed on "Integrated with PrediX" page

### 6.3 Portfolio tracker (Zapper, DeBank style)

**Goal**: Show PrediX positions in user's portfolio view.

**Pattern:**

1. **Setup**
   - Request Partner tier (read-only)
   - Subscribe to relevant addresses via webhook or bulk query
   
2. **Data sync**
   - Periodic sync: `GET /api/v2/users/:address/portfolio` for tracked users
   - Real-time: webhook on `orders.filled` + `redemptions.claimed`
   - Historical: bulk `/partner/history/trades?user=<addr>`
   
3. **Display**
   - Show outcome tokens with $USD value (from current YES price)
   - Show unrealized PnL (current value - cost basis)
   - Show resolvable / claimable positions

### 6.4 Oracle / data provider integration (Phase 2+)

Phase 2 UMA adoption opens opportunities:

- **UMA proposer service**: partner monitors markets + auto-proposes outcomes with bond → earn reward fees
- **UMA disputer service**: partner reviews proposals + disputes wrong ones → earn dispute reward
- Both are permissionless (anyone can participate) — partners with good reputation + speed win most rewards

## 7. SLA & support tiers

### 7.1 SLA commitments

| Tier | API uptime | P1 response | P2 response | Escalation path |
|---|---|---|---|---|
| Public | Best effort | n/a | n/a | Community Discord |
| Developer | 99.5% | < 24h (email) | < 72h | Email `support@predixpro.io` |
| Partner | 99.9% | < 1h (Slack) | < 4h | Dedicated Slack channel + email |
| Institutional | 99.95% + custom | < 15min (paged) | < 1h | 24/7 on-call + account manager |

### 7.2 Partner-specific data

Partners receive:

- Monthly uptime report
- Incident post-mortems (private copy)
- Roadmap preview (30-60 days ahead)
- Preview of breaking changes (90 days notice)

## 8. Deprecation policy

Partner API **breaking changes** get 90-day notice:

1. **T-90 days**: Announcement in partner Slack + email + docs
2. **T-60 days**: Mandatory migration guide published
3. **T-30 days**: Warning response header `Sunset: <date>`
4. **T-0**: Endpoint returns 410 Gone with migration instructions

Versioning: URL-based (`/api/v2/*` → `/api/v3/*`). Parallel operation during migration.

## 9. Security considerations for partners

### 9.1 API key security

**Partner responsibilities:**

- Store API key in secure vault (AWS Secrets Manager, HashiCorp Vault, GCP Secret Manager)
- Never commit to git — use environment variables
- Rotate quarterly (admin panel)
- Separate keys per environment (prod/staging)

**Compromised key**: immediately revoke in admin panel. No key-level liability (partner not held responsible for charges pre-revocation if reported promptly).

### 9.2 Webhook security

**Partner server:**

- Verify HMAC signature on every webhook delivery (`X-Predix-Signature`)
- Use shared secret NOT exposed in URL/logs
- Reject messages with mismatched sig or stale timestamp (> 5 min old)
- Use HTTPS only for webhook URL

**Sample verification (TypeScript):**

```typescript
import { createHmac } from 'crypto';

function verifyWebhook(body: string, signature: string, secret: string): boolean {
  const expected = createHmac('sha256', secret).update(body).digest('hex');
  return signature === expected;
}
```

### 9.3 Rate limit compliance

Partners should:

- Monitor `X-RateLimit-Remaining` header
- Implement exponential backoff on 429
- Never retry aggressive (floods BE queue)
- Coordinate bulk queries to off-peak windows if feasible

Abuse → rate limit temporarily reduced, then reinstated after discussion.

## 10. Code examples

### 10.1 Partner WebSocket client (TypeScript)

```typescript
import WebSocket from 'ws';

const ws = new WebSocket('wss://partner-ws.predixpro.io/v2', {
  headers: { 'X-API-Key': process.env.PREDIX_API_KEY },
});

ws.on('open', () => {
  ws.send(JSON.stringify({
    action: 'subscribe',
    channels: ['trades', 'orderbook:42', 'orders:user:0xabc...'],
  }));
});

ws.on('message', (data) => {
  const event = JSON.parse(data.toString());
  if (event.channel === 'trades') {
    console.log('Trade:', event.data);
    // handle
  }
});

ws.on('close', (code, reason) => {
  console.log('WS closed, reconnecting...');
  // reconnect logic with exponential backoff
});
```

### 10.2 Webhook receiver (Express/Node)

```typescript
import express from 'express';
import { createHmac } from 'crypto';

const app = express();
app.use(express.raw({ type: 'application/json' }));

const WEBHOOK_SECRET = process.env.PREDIX_WEBHOOK_SECRET!;

app.post('/predix/webhook', (req, res) => {
  const signature = req.header('X-Predix-Signature');
  const expectedSig = createHmac('sha256', WEBHOOK_SECRET)
    .update(req.body)
    .digest('hex');
    
  if (signature !== expectedSig) {
    return res.status(401).send('Invalid signature');
  }
  
  const event = JSON.parse(req.body.toString());
  console.log('Event:', event.event, event.data);
  
  // Idempotency check via X-Predix-Id
  if (alreadyProcessed(req.header('X-Predix-Id'))) {
    return res.status(200).send('OK (dup)');
  }
  
  // Process
  processEvent(event);
  res.status(200).send('OK');
});

app.listen(3000);
```

### 10.3 Bulk history fetch (for analytics)

```typescript
async function fetchTradesForPeriod(since: number, until: number) {
  const apiKey = process.env.PREDIX_API_KEY!;
  let cursor: string | null = null;
  const allTrades = [];
  
  do {
    const params = new URLSearchParams({ since: String(since), until: String(until), limit: '10000' });
    if (cursor) params.set('cursor', cursor);
    
    const res = await fetch(`https://api.predixpro.io/api/v2/partner/history/trades?${params}`, {
      headers: { 'X-API-Key': apiKey, 'Accept-Encoding': 'gzip' },
    });
    const { data, meta } = await res.json();
    
    allTrades.push(...data.data);
    cursor = data.meta.next_cursor;
  } while (cursor);
  
  return allTrades;
}
```

## 11. Onboarding process

### 11.1 Application

1. Email `partnerships@predixpro.io` with business use case
2. Quick call (30 min) with partnerships team
3. Complete onboarding form:
   - Company legal name
   - Primary contact (name, email, Slack/TG)
   - Integration use case
   - Projected volume + request rate
   - Security commitments (webhook handling, key storage)
4. KYB review (1-2 weeks)
5. API key issued upon approval

### 11.2 Onboarding checklist (partner side)

- [ ] Received API key, stored securely
- [ ] Integrated REST endpoints
- [ ] (Optional) WebSocket integration + reconnect logic
- [ ] (Optional) Webhook receiver with signature verification
- [ ] Rate limit respectful implementation
- [ ] Error handling for 4xx/5xx
- [ ] Monitoring + alerting on integration health
- [ ] Test environment verified before prod
- [ ] Security review sign-off (partner's internal)

### 11.3 Go-live

Partner notifies PrediX partnerships team → joint smoke test → announcement (Discord + Twitter co-post optional).

## 12. Pricing model

### 12.1 Phase 1-2 (bootstrap, 2026)

**Developer tier**: Free forever (no commercial use commitment required)
**Partner tier**: Free during bootstrap period, revenue share or sliding scale commercial agreement

### 12.2 Phase 3+ (2027+)

Pricing to be determined based on:

- Protocol token launch + governance decision
- Community vote on API monetization
- Cost recovery for infrastructure

**Commitment**: Fair + transparent pricing. No bait-and-switch. 90-day advance notice for any change.

## 13. Rate limit allocation

### 13.1 Burst handling

Burst size > sustained rate allowed:

- Public: 1000/min sustained, 100 burst capacity (short spike OK)
- Developer: 5K/min sustained, 500 burst
- Partner: 50K/min sustained, 5K burst

Token bucket algorithm. Tokens regenerate at sustained rate; unused tokens accumulate up to burst cap.

### 13.2 Concurrent requests

Partners should use connection pooling:

- HTTP/2 with multiplexing preferred
- Max 50 concurrent connections per key
- Keep-alive recommended

## 14. Changelog

- **v1.0** — 2026-04: Initial design. Rollout Q2 2026 post-mainnet.

## 15. References

- [Backend API Overview](../backend-api/00-overview.md) — public baseline
- [Integration Patterns](../developers/42-integration-patterns.md) — developer guides
- [Rate Limits & Caching](../backend-api/04-rate-limits-caching.md) — public tier details
- [System Architecture](00-system-architecture.md)
- Contact: `partnerships@predixpro.io`
