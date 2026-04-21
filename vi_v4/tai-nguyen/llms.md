---
description: Cô đọng docs PrediX cho AI agent / LLM ingest. Single-page summary để paste vào context window của mọi LLM assistant.
---

# llms.txt (for AI agents)

{% hint style="info" %}
Trang này cung cấp single-page summary cho AI coding assistants + LLM agents. Paste toàn bộ content vào context window của agent khi giao task liên quan PrediX. Format theo [llmstxt.org](https://llmstxt.org/) spec khuyến nghị.
{% endhint %}

---

```
# PrediX V2

> PrediX V2 là prediction-market protocol on-chain trên Unichain (Chain ID 1301 testnet, 130 mainnet TBA), hybrid AMM + CLOB liquidity, outcome tokens ERC-20, non-custodial.

## One-liner

Users buy YES/NO outcome tokens with USDC to predict real-world events. YES + NO ≈ $1 per pair. Winning side redeems $1 per token after oracle resolution.

## Architecture

- Smart Contracts (Solidity 0.8.30, Foundry, evm=cancun) trong SC/packages: shared, oracle, diamond, hook, exchange, router
- Diamond = EIP-2535 proxy với 6 facets: MarketFacet, EventFacet, AccessControlFacet, PausableFacet, DiamondCutFacet, DiamondLoupeFacet (KHÔNG có OwnableFacet)
- Hook = PrediXHookV2 (v4 hook) + PrediXHookProxyV2 (ERC1967 + 48h timelock)
- Exchange = PrediXExchange CLOB với 3 MatchType: COMPLEMENTARY=0, MINT=1, MERGE=2
- Router = PrediXRouter stateless aggregator, Permit2-native, zero-custody
- Oracle = pluggable IOracle; adapters: ManualOracle + ChainlinkOracle (round-pinned, staleness 24h, L2 sequencer check)
- Indexer = Ponder 0.16.6 → PostgreSQL 28 tables → Hono REST /api/*
- Backend = NestJS + Fastify + zod + Mongoose + lru-cache + viem (SIWE), OpenAPI auto-generated
- Frontend = Next.js 16 + React 19 + Tailwind 4 + Ant Design + Wagmi 2 + Viem 2 + RainbowKit + ZeroDev passkey

## Key enums

Side (Exchange + TradeType): 0=BUY_YES, 1=SELL_YES, 2=BUY_NO, 3=SELL_NO
MatchType: 0=COMPLEMENTARY, 1=MINT, 2=MERGE
Comparison (ChainlinkOracle): 0=GT, 1=GTE, 2=LT, 3=LTE

## Roles

DEFAULT_ADMIN_ROLE = 0x00 — multisig
ADMIN_ROLE = keccak256("predix.role.admin") — create market, fee config
OPERATOR_ROLE = keccak256("predix.role.operator") — emergency resolve (+7d), enable refund mode
PAUSER_ROLE = keccak256("predix.role.pauser") — pause/unpause module
CUT_EXECUTOR_ROLE = keccak256("predix.role.cut_executor") — ONLY TimelockController holds; DEFAULT_ADMIN cannot bypass

## Modules (pause keys)

keccak256("predix.module.market") — MARKET
keccak256("predix.module.diamond") — DIAMOND

## Constants

EMERGENCY_DELAY = 7 days (for operator emergencyResolve)
GRACE_PERIOD = 365 days (before admin sweepUnclaimed)
MAX_REDEMPTION_FEE_BPS = 1500 (15% hard cap)
BPS_DENOMINATOR = 10000
Price range = 10000 to 990000 ($0.01-$0.99, step 10000)
MIN_ORDER_SIZE = 1000000 ($1)
MAX_ORDERS_PER_USER_PER_MARKET = 50
MAX_FILLS_PER_PLACE = 20

## 7 Critical Invariants

INV-1: Collateral solvency — YES.totalSupply == NO.totalSupply == market.totalCollateral (unresolved market)
INV-2: Exchange solvency — Σ order.depositLocked == USDC.balanceOf(exchange) + Σ outcomeToken.balanceOf(exchange)
INV-3: Router non-custody — USDC/YES/NO balanceOf(router) == 0 post-call
INV-4: Redemption fee ≤ MAX_REDEMPTION_FEE_BPS (1500)
INV-5: Hook identity commit required in beforeSwap (EIP-1153 transient)
INV-6: Resolution monotonic — isResolved=true is immutable once set
INV-7: OutcomeToken mint/burn only by Diamond (onlyFactory)

## Key audit findings (remediated)

FINAL-H04: Redemption fee snapshot at createMarket (admin cannot raise retroactive)
FINAL-D-03: Oracle re-verify at resolveMarket (revoke compromised oracle prevents resolve)
NEW-01: CUT_EXECUTOR_ROLE only TimelockController, DEFAULT_ADMIN cannot bypass 48h upgrade
H-H03: V2 Hook uses identity commit EIP-1153, replaces V1 (block, direction) anti-sandwich

## Deployed Addresses (Unichain Sepolia, Chain ID 1301)

Diamond:          0xF38a265E6e4F57D000a1CC08004da5B4A380B08A
Hook V2 Proxy:    0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0
Exchange:         0xa202abCb2A358c0862B2dA76b553398339F2C638
Router:           0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7
ManualOracle:     0x699A8C74663b1C852E195b2ffa00D5965E992Cf3
USDC (testnet):   0x12fd156C8b5F2901BA2781d97db84AaC56b2b911
PoolManager v4:   0x00B036B58a818B1BC34d502D3fE730Db729e62AC
Permit2:          0x000000000022D473030F116dDEE9F6B43aC78BA3

RPC: https://sepolia.unichain.org
Explorer: https://sepolia.uniscan.xyz

## Mainnet (Unichain, Chain ID 130) — TBA, pending external audit

## BE REST API v2 (/api/v2/*)

Envelope: success {data, meta: {timestamp, version}}; error {error: {code, message, details}, meta}
Cache: hot=2s, warm=60s (only 2 tiers)
Auth: SIWE (EIP-4361) challenge → verify → session token 24h

### Key endpoints

Markets: GET /api/v2/markets (kind, status, eventId, tag, q, sortBy, limit, offset)
Market detail: GET /api/v2/markets/{id}
Orderbook: GET /api/v2/markets/{id}/orderbook?depth=20
Trades: GET /api/v2/markets/{id}/trades
Prices + Candles: /markets/{id}/prices, /candles
Holders: /markets/{id}/holders
Activity: /markets/{id}/activity
Pricing quote: /markets/{id}/pricing/quote?side=YES&action=buy&amountIn=100
Pricing view: /markets/{id}/pricing/view
Batch pricing: GET /api/v2/markets-batch/price-views?ids=...
Events: GET /api/v2/events, /events/{id}
Users: /users/{address}/portfolio, /positions/{marketId}, /orders, /trades, /pnl, /history, /profile
Auth: POST /auth/challenge, /auth/verify, /auth/logout; GET /auth/me
Stats: GET /api/v2/stats
Leaderboard: GET /api/v2/leaderboard
Traders: GET /api/v2/traders, /traders/{address}
Rewards: /rewards/boxes, /challenges, /multiplier
Admin: /admin/markets/{id}/{display,pause,resume,hide,unhide}, /admin/events/*, /admin/market-metadata, /admin/role-changes, /admin/pauses, /admin/diamond-cuts, /admin/fee-changes, /admin/hook-proxy-upgrades

### Error codes (closed enum)

invalid_request (400), auth_required (401), forbidden (403), not_found (404), market_not_found (404), rate_limit_exceeded (429), internal_error (500), indexer_unavailable (503)

### Primitives

Address: lowercase 42-char hex (NEVER checksum, NEVER mixed)
marketId: bigint decimal string OR 66-char hex (endpoint-specific)
Price: decimal string "0.524" (NEVER JS number)
MoneyAmount: {decimal, raw, decimals, unit}
Timestamp: unix seconds integer (NEVER ISO, NEVER millis)
LocalizedString: {key, fallback}
SemanticToken: {tokenKey?, literal?}
Status: computed server-side, FE uses capabilities[] for gating
Discriminated union: market.kind='binary'|'multi'|..., access via market[market.kind]

## Indexer

13 contracts subscribed; 28 tables. Router.Trade = canonical volume source (no double-count with Hook_MarketTraded or OrderMatched).

OutcomeToken Transfer events auto-subscribed via factory pattern từ MarketCreated block forward.

Reorg: Ponder 0.16 uses Unichain `finalized` block tag (~12-15 min L2 finality). No user-tunable override in 0.16.6.

isProtocolContract() filter: Diamond/Router/Exchange/HookProxy/PoolManager addresses skip position/user writes (avoid phantom user rows).

### Indexer REST /api/*

/api/markets, /api/markets/{id}, /api/markets/{id}/trades
/api/events, /api/events/{id}
/api/stats (flat, no envelope)
/api/portfolio/{address}
/api/leaderboard

Reserved by Ponder: /health, /ready, /status, /metrics, /graphql

## Events (canonical names)

Diamond MarketFacet: MarketCreated, PositionSplit, PositionMerged, TokensRedeemed, MarketResolved, MarketEmergencyResolved, RefundModeEnabled, MarketRefunded, UnclaimedSwept, OracleApproved, OracleRevoked, FeeRecipientUpdated, MarketCreationFeeUpdated, DefaultPerMarketCapUpdated, PerMarketCapUpdated, DefaultRedemptionFeeUpdated, PerMarketRedemptionFeeUpdated

Diamond EventFacet: EventCreated, EventResolved, EventRefundModeEnabled

Diamond AccessControl: RoleGranted, RoleRevoked, RoleAdminChanged

Diamond Pausable: ModulePaused, ModuleUnpaused

Diamond DiamondCut: DiamondCut(FacetCut[], init, calldata)

Exchange: OrderPlaced, OrderMatched(MatchType), OrderCancelled, FeeCollected

Router: Trade(uint8 tradeType), DustRefunded (no-op)

Hook V2: Hook_PoolRegistered, Hook_MarketTraded, Hook_TrustedRouterUpdated, Hook_AdminChangeProposed, Hook_AdminUpdated, Hook_PauseStatusChanged

Hook Proxy V2: UpgradeProposed, Upgraded, UpgradeCancelled, AdminChangeProposed, AdminChanged, TimelockDurationUpdated, InitReverted

ManualOracle: ResolutionSet, AdminChanged
ChainlinkOracle: MarketBound, Resolved

## Integration patterns

### Place trade (Router, NOT through BE)

```
// Approve USDC to Router (one-time with MaxUint256)
USDC.approve(ROUTER, MAX_UINT256)

// Quote (revert-and-decode)
Router.quoteBuyYes(marketId, usdcIn) reverts with abi.encode(uint256 yesOut)

// Execute
Router.buyYes(marketId, usdcIn, minYesOut, recipient, deadline)
```

### Place limit order (Exchange)

```
USDC.approve(EXCHANGE, MAX_UINT256)
Exchange.placeOrder(marketId, Side side, price, amount) → (orderId, filledAmount)
Exchange.cancelOrder(orderId)
```

### Split / Merge / Redeem (Diamond)

```
Diamond.splitPosition(marketId, amount)   // USDC → YES + NO
Diamond.mergePositions(marketId, amount)  // YES + NO → USDC
Diamond.resolveMarket(marketId)           // public, after oracle ready
Diamond.redeemMarketTokens(marketId)      // burn winners, USDC - fee
Diamond.refund(marketId, amount)          // only in refund mode
```

### Event listening (viem)

```
publicClient.watchContractEvent({
  address: ROUTER, abi: RouterAbi,
  eventName: 'Trade',
  args: { marketId, trader },
  onLogs: handleLogs,
});
```

### Auth (SIWE)

```
GET /api/v2/auth/challenge?address=0x... → { message, nonce }
// user signs message with wallet
POST /api/v2/auth/verify { address, signature, nonce } → { sessionToken }
// use: Authorization: Bearer <sessionToken>
```

## Tech stack versions

- Solidity 0.8.30
- Foundry (via_ir, evm=cancun, optimizer runs=200)
- OpenZeppelin Contracts, Uniswap v4-core, v4-periphery, Permit2, Chainlink AggregatorV3Interface
- Ponder 0.16.6, PostgreSQL 16, Hono 4.5, vitest 4, pnpm 10
- NestJS 11, Fastify 5, zod 4, Mongoose 9, lru-cache 11, viem 2.47, Vitest 4, TypeScript 5.9.3 exact
- Next.js 16.2, React 19.2, Tailwind 4, Ant Design 6, Wagmi 2.19, Viem 2.47, RainbowKit 2.2

## Forbidden packages (BE)

class-validator, class-transformer, axios, node-cache, ethers, jest, ts-jest, supertest, eslint, prettier, ts-node

Replace with: zod + nestjs-zod, native fetch/undici, lru-cache, viem, vitest, biome, tsx

## Key security features

- TransientReentrancyGuard (EIP-1153) on 20+ external functions
- Identity commit via EIP-1153 transient storage (replaces V1 block+direction sandwich detection)
- 48h TimelockController for diamondCut
- 48h custom timelock for Hook proxy upgrade
- Oracle approval list + re-verify at resolve (FINAL-D-03)
- Redemption fee snapshot at create (FINAL-H04)
- Pause asymmetry: maker path gated, taker path always open

## Contact

- Website: predixpro.io
- App (testnet): app-testnet.predixpro.io
- Docs: docs.predixpro.io
- GitHub: github.com/predix-protocol
- Email: info@predixpro.io (general), security@predixpro.io (security)
- Discord: discord.gg/predixpro
- Twitter: @PrediXPro

## Common mistakes by AI agents (avoid these)

1. DO NOT reference `PrediXHookV1` — canonical is `PrediXHookV2`
2. DO NOT reference `OwnableFacet` — does not exist; roles are role-based
3. DO NOT call "event group" as "category" — canonical is "Event" / "EventFacet"
4. DO NOT claim 4 MatchTypes — only 3 (COMPLEMENTARY, MINT, MERGE); price improvement is a rule, not a type
5. DO NOT say sandwich detection uses (block.number, direction) — V2 uses identity commit EIP-1153
6. DO NOT say 4 roles — there are 5 (include CUT_EXECUTOR_ROLE)
7. DO NOT say admin can upgrade directly — must go through TimelockController 48h
8. DO NOT say redemption fee can be raised after market creation — snapshotted at create (FINAL-H04)
9. Price/money = decimal string, NOT JS number
10. Address = lowercase, NOT checksum
11. Timestamp = unix seconds integer, NOT ISO, NOT millis
12. `/api/v2/*` for BE public API, NOT `/api/v1`
```

---

## Cho AI agent sử dụng

Nếu bạn là một LLM coding assistant được giao task liên quan PrediX:

1. **Start here**: copy toàn bộ code block trên vào context
2. Nếu task SC: cũng xem [Contract Architecture](../smart-contracts/27-contract-architecture.md) + [Custom Errors](../smart-contracts/custom-errors.md)
3. Nếu task BE: cũng xem [Backend API Overview](../backend-api/00-overview.md)
4. Nếu task integration: cũng xem [Integration Patterns](../developers/42-integration-patterns.md)

File này được maintain alongside code changes — version align với [Changelog](changelog.md).

## Raw download

Copy nội dung trong code block → paste vào AI. GitBook export PDF cũng include.

Nếu build CI/CD, fetch trang này qua raw markdown:

```
https://docs.predixpro.io/vi_v4/tai-nguyen/llms.md (markdown export)
```

(GitBook public export endpoint TBA — hiện tại copy manual từ code block).
