# Remaining SVG Prompts — 43 Mermaid blocks

Dùng chung **Design System** từ `diagram-prompts.md` §1 cho mọi prompt dưới đây.

> **Duplicate note**: Một số diagram xuất hiện ở 2 file (khai-niem + giao-thuc). Tạo 1 SVG, dùng cho cả 2.

---

## Group A — khai-niem/ (Concepts) — 10 SVGs

### A1. Fee pie chart (adaptive) `→ khai-niem/phi.md:7`
**File**: `31-fee-distribution-pie.svg`

```
Donut chart — PrediX fee distribution (adaptive 4-phase).

Center text: "Protocol fee" (16px bold)

Segments (show RANGES, not fixed):
- Treasury: 15-60% — color #f59e0b
- Staker (USDC yield): 20-35% — color #2563eb
- Buyback-burn: 15-50% — color #dc2626
- Insurance: 5% — color #22c55e

Legend below: color dot + label + range %
Subtitle under chart: "Phase-dependent: Bootstrap → Scale → Mature → Dominance"
```

### A2. CLOB fee tiers `→ khai-niem/phi.md:35`
**File**: `32-clob-fee-tiers.svg`

```
Horizontal flow diagram — CLOB fee schedule by time to market end.

Left side: 2 user types stacked:
- "Maker" → arrow → "0% fee always" (green badge)
- "Taker" → arrow → decision diamond "Time to end?"

From decision, 4 branches right:
- > 7 days: "0% bootstrap" (green)
- 3-7 days: "0.5%" (gray)
- 1-3 days: "0.75%" (gray)
- < 24h: "1.0% cap" (red)

Horizontal layout, clean arrows, color-coded severity.
```

### A3. Adaptive fee split flow `→ khai-niem/phi.md:105`
**File**: `33-adaptive-fee-split.svg`

```
Flow diagram — Protocol fee → adaptive split into 4 buckets.

Left: "Protocol fee" source node (dark)
Center: "Adaptive split" decision node
Right: 4 destination nodes in a vertical stack:
- Treasury (15-60%) — amber/orange
- Staker USDC yield (20-35%) — blue
- Buyback + burn PRX (15-50%) — red
- Insurance fund (5%) — green

Arrows from center to each, with % range labels on arrows.
Clean horizontal LR layout.
```

### A4. 3 match types — CLOB `→ khai-niem/clob-va-amm.md:31`
**File**: `34-clob-3-match-types.svg`

```
3-row comparison diagram — CLOB matching engine types.

Row 1 "Complementary" (most common):
  BUY_YES @ $0.50 ←match→ SELL_YES @ $0.48
  Note: "Taker gets better price"

Row 2 "Mint (synthetic)":
  BUY_YES @ $0.60 + BUY_NO @ $0.45 = $1.05 ≥ $1
  Note: "Diamond mints pair, spread $0.05 → protocol"

Row 3 "Merge (synthetic)":
  SELL_YES @ $0.40 + SELL_NO @ $0.45 = $0.85 ≤ $1
  Note: "Diamond burns pair, spread $0.15 → protocol"

Each row: left-right flow with colored nodes, separated by horizontal line.
Green for complementary, blue for mint, orange for merge.
```

### A5. MEV protection flow `→ khai-niem/clob-va-amm.md:101`
**File**: `35-mev-protection.svg`

```
Vertical flow — anti-sandwich identity commit mechanism.

Steps:
1. [START blue] User submits buyYes tx
2. [STEP] Router commits swap identity (transient storage EIP-1153)
3. [STEP] Router calls poolManager.swap()
4. [STEP] PoolManager calls Hook.beforeSwap()
5. [DECISION] Hook verifies identity match?
   - ✅ Match → [STEP] Swap proceeds → [END green] Token out to user
   - ❌ No identity (sandwich attacker) → [END red] Revert, attacker fails

Color: green path for legitimate, red for attacker revert.
```

### A6. Oracle types overview `→ khai-niem/resolution.md:21`
**File**: `36-oracle-types.svg` — **SHARED with giao-thuc/oracle.md:22 (reuse)**

```
Decision tree — which oracle for which event type.

Top: "Event type?" decision diamond
4 branches:
- Price threshold (BTC > $100k?) → "ChainlinkOracle" (green, auto, permissionless)
- Subjective (who wins election?) → "ManualOracle" (blue, multisig 3/5)
- Decentralized (no multisig trust) → "UMAOracle" (gray, 48h dispute window, Phase 2)
- Cross-chain / governance → "Committee oracle" (blue, t-of-N validator)

Each oracle node: name + 1-line description. Clean vertical TD layout.
```

### A7. ChainlinkOracle flow `→ khai-niem/resolution.md:41`
**File**: `37-chainlink-oracle-flow.svg` — **SHARED with giao-thuc/oracle.md:22**

```
Vertical process flow — Chainlink oracle resolution.

Steps:
1. [START blue] Creator registers oracle (feed=BTC/USD, threshold=$100k, snapshotAt=endTime)
2. [WAIT gray] ...endTime passes...
3. [START blue] Anyone calls resolve(marketId, roundIdHint, prevHint)
4. [STEP] Oracle reads Chainlink getRoundData → { answer, updatedAt }
5. [STEP] Oracle verifies 3 conditions:
   • updatedAt ≥ snapshotAt
   • prev.updatedAt < snapshotAt (adjacency)
   • L2 sequencer uptime OK
6. [STEP] Compute outcome = (answer ≥ threshold)
7. [END green] MarketResolved event emitted

Clean vertical flow, 7 steps.
```

### A8. UMAOracle flow `→ khai-niem/resolution.md:82`
**File**: `38-uma-oracle-flow.svg` — **SHARED with giao-thuc/oracle.md:104**

```
Vertical flow with branch — UMA optimistic oracle dispute mechanism.

Steps:
1. [START blue] Proposer: propose(outcome, bond)
2. [WAIT gray] 48h dispute window
3. [DECISION] Anyone disputes?
   - No → [STEP] Bond refunded, outcome finalized → [END green] Market resolved
   - Yes → [STEP red] Disputer posts bond → UMA DVM votes → Loser loses bond → [END green] Market resolved

Branch at step 3 is key visual. Phase 2 badge in corner.
```

### A9. Refund mode flow `→ khai-niem/resolution.md:132`
**File**: `39-refund-mode.svg` — **SHARED with giao-thuc/oracle.md:169**

```
Horizontal flow — refund mode as last resort.

5 nodes left to right:
1. [RED] Oracle fails (feed down / multisig unresponsive / dispute hung)
2. [GRAY] Admin proposes enableRefundMode
3. [GRAY] TimelockController 48h delay
4. [BLUE] refundModeActive = true
5. [GREEN] User burns min(YES,NO) pairs → receives USDC pro-rata

Horizontal LR layout, red-to-green gradient flow.
```

### A10. Outcome token diagrams (4 small) `→ khai-niem/outcome-tokens.md`
**File**: `40-outcome-tokens.svg` (combined 4-in-1)

```
Combined diagram — 4 panels in 2×2 grid:

Panel 1 (top-left) "$1 Invariant":
  100 USDC → Diamond mint → 100 YES + 100 NO
  Dotted: YES wins → Redeem 100 USDC

Panel 2 (top-right) "Split":
  User → deposit 100 USDC → Diamond → mint 100 YES + 100 NO → User

Panel 3 (bottom-left) "Merge":
  User → burn 100 YES + 100 NO → Diamond → payout 100 USDC → User

Panel 4 (bottom-right) "Arbitrage $1 peg":
  If YES+NO > $1: Split USDC → sell both → profit
  If YES+NO < $1: Buy both → merge → profit
  Arrow to center: "Pulled back to ~$1"

Each panel: small title, simple 3-4 node flow. Light border separating panels.
```

---

## Group B — huong-dan/ (Guides) — 14 SVGs

### B1. Chart data sources `→ huong-dan/chart-va-timeframe.md:7`
**File**: `41-chart-sources.svg`

```
Horizontal merge flow — 3 data sources combine into chart.

3 source nodes (left, stacked vertical):
- "Router.Trade events" (canonical price) — blue
- "Hook AMM swaps" (analytics price) — blue
- "CLOB matches" (price ticks) — blue

All 3 → merge into "Combined chart" node → "YES price over time" output (green)
Simple LR flow.
```

### B2. Notification channels `→ huong-dan/notifications.md:7`
**File**: `42-notification-channels.svg`

```
Fan-out flow — event to 5 notification channels.

Left: "PrediX Event" source node
Center: "Notification filter" node
Right: 5 channel nodes fanned out:
- Push notification (browser/mobile)
- Email digest (daily/weekly)
- Discord webhook
- Telegram bot
- In-app notification center

Simple LR fan-out.
```

### B3. Price alert flow `→ huong-dan/notifications.md:73`
**File**: `43-price-alert-flow.svg`

```
Vertical flow with loop — price alert setup + trigger.

1. [START] User sets alert: "BTC market YES > $0.70"
2. [STEP] App subscribes to price stream via Indexer
3. [LOOP] Every block: check alert conditions
4. [DECISION] Condition met?
   - No → back to loop
   - Yes → [STEP] Notification service fires alert
5. [END green] User receives push + email + in-app
```

### B4. Portfolio overview `→ huong-dan/portfolio.md:7`
**File**: `44-portfolio-overview.svg`

```
Hub-and-spoke — portfolio page layout.

Top: "Hero KPIs" bar (Total value · Realized PnL · Unrealized PnL · Win rate · Accuracy)
Below: 6 tab nodes arranged horizontally:
- Active positions
- Open orders
- Liquidity (LP)
- History
- Calibration
- Performance replay

Clean horizontal tab bar layout, hero on top.
```

### B5. PnL calculation `→ huong-dan/portfolio.md:38`
**File**: `45-pnl-calc.svg`

```
Flow diagram — realized vs unrealized P&L example.

Left: "Buy 100 YES @ $0.50 = 50 USDC spent"
Branch:
- Sell 40 @ $0.60 = 24 USDC → "Realized: +$4 profit" (green)
- Hold 60 YES, cost basis $0.50 → spot $0.55 → "Unrealized: +$3" (gray)

Simple branching flow with green (realized) and gray (unrealized) end nodes.
```

### B6. Badge categories `→ huong-dan/portfolio.md:111`
**File**: Already have `28-mindmap-badges.svg` — **REUSE** (same content)

### B7. Market creation decision `→ huong-dan/tao-market.md:16`
**File**: `46-market-creation-decision.svg`

```
Decision tree — what type of market to create.

Top: "Event type?" decision diamond
4 branches:
- Price threshold (BTC > $X?) → "Binary + ChainlinkOracle"
- Yes/No subjective (who wins?) → "Binary + ManualOracle"
- Multi-outcome (tournament winner?) → "Event with N members"
- Range value (GDP?) → "Scalar market"

All 4 → converge to "Setup parameters" → "Deploy on-chain"
```

### B8. Discussion features `→ huong-dan/discussion.md:9`
**File**: `47-discussion-features.svg`

```
Hub diagram — market discussion features.

Center: "Market Discussion tab"
5 feature nodes around it:
- Comment list (sort: newest / top voted)
- Post new comment
- Reply threading (2 levels)
- Up/down vote
- @mention users

Simple hub-spoke layout.
```

### B9. Social feed `→ huong-dan/discussion.md:53`
**File**: `48-social-feed.svg`

```
Merge + filter flow.

Left: 2 sources stacked:
- "On-chain activity" (Trade, LP, Resolve)
- "Off-chain activity" (Comment, Follow, Badge)
Both → "Social Feed" → Filter (Following / Trending / Latest) → "Display cards (realtime)"

Simple LR flow.
```

### B10. Moderation flow `→ huong-dan/discussion.md:123`
**File**: `49-moderation-flow.svg`

```
Vertical flow — content moderation.

1. [RED] Spam / inappropriate content detected
2. [STEP] User reports
3. [STEP] Community mod reviews
4. [DECISION] Valid report?
   - Yes → [STEP] Hide content + warn user → [DECISION] Repeat offender (3+)?
     - Yes → [RED] Ban 7-30 days
   - No → [STEP] Dismiss + warn reporter if abuse
```

### B11. Trader profile `→ huong-dan/leaderboard.md:26`
**File**: `50-trader-profile.svg`

```
Hub layout — public trader profile page.

Center: "Profile address"
6 sections:
- Hero KPIs (Volume · PnL · Win rate · Accuracy)
- Active positions (public)
- Trade history (public)
- Calibration chart
- Badges earned
- Follow button

Simple hub with sections, like portfolio but public view.
```

### B12. Copy trading flow `→ huong-dan/leaderboard.md:64`
**File**: `51-copy-trading-flow.svg`

```
Vertical flow — copy trading setup + execution.

1. [CONFIG] Follower setup: Copy 10% size, max $100/trade
2. [START blue] Lead trader executes: Buy 100 YES BTC market
3. [STEP] Copy Engine detects tx
4. [STEP] Calculate copy size = 10% × lead balance, cap $100
5. [DECISION] Mode?
   - Auto → [STEP] Auto-execute mirror trade
   - Manual → [STEP] Notify follower → user confirms
6. [END green] Trade mirrored on follower account
```

### B13. LP risk vs reward `→ huong-dan/cung-cap-thanh-khoan.md:14`
**File**: `52-lp-risk-reward.svg`

```
Branching flow — LP outcomes after market resolve.

Top: "Provide LP (YES + USDC)"
Branch to 2 outcomes:
- [GREEN] "Earn fee every swap" (positive)
- [RED] "Impermanent loss if price moves" (risk)

Then: "After resolve?" decision:
- YES wins → YES=$1, USDC stays
- NO wins → YES=$0, USDC stays
Both → "Net P&L = final value - initial + fees earned"
```

### B14. Event multi-outcome structure `→ huong-dan/event-multi-outcome.md`
**File**: `53-event-multi-outcome.svg` (combined 2 diagrams)

```
2 panels:

Panel 1 "Event structure":
  "Event: FIFA WC 2026" → branches to N members:
  - Member 1: Argentina (YES+NO tokens)
  - Member 2: Brazil (YES+NO tokens)
  - Member 3: France (YES+NO tokens)
  - ... 48 members
  Resolve: winningIndex=0 → Argentina YES=$1, rest=$0

Panel 2 "Buy flow":
  Click member in UI → Trading panel opens → Buy YES Argentina
  → Router swaps USDC → YES via CLOB+AMM → User receives YES

Simple, 2 panels stacked vertically.
```

---

## Group C — giao-thuc/ (Protocol) — 4 SVGs

### C1. Diamond facet upgrade `→ giao-thuc/bao-mat.md:57`
**File**: `54-diamond-upgrade-flow.svg`

```
Vertical flow — Diamond facet upgrade with 48h timelock.

1. [START blue] Multisig 3/5 schedules diamondCut (delay=48h)
2. [STEP] TimelockController emits CallScheduled event
3. [WAIT red] 48h challenge window — community verifies facet on explorer
4. [STEP] Multisig executes after 48h
5. [STEP] Timelock calls Diamond.diamondCut(facets[])
6. [END green] DiamondCut event emitted, facets updated
```

### C2. Incident severity tiers `→ giao-thuc/bao-mat.md:136`
**File**: `55-severity-tiers.svg`

```
Decision tree — incident severity classification.

Top: "Issue detected" → "Severity?" decision
4 branches:
- P0 Critical (fund risk): "On-call page <15min · Pause module · Public disclosure 72h" — RED
- P1 High (degradation): "Alert <1h · Fix prioritize" — dark gray
- P2 Medium: "Slack <24h · Schedule fix" — blue
- P3 Low/Info: "Backlog · Next sprint" — blue

Color-coded by severity. Clean TD layout.
```

### C3. ManualOracle flow `→ giao-thuc/oracle.md:65`
**File**: `56-manual-oracle-flow.svg`

```
Vertical flow — manual oracle resolution by multisig.

1. [START blue] Real-world event resolves (e.g. Argentina wins WC)
2. [STEP] Multisig verifies from ≥2 sources (Reuters, AP, official API)
3. [STEP] Multisig 3/5 signs ManualOracle.report(marketId, outcome=true)
4. [STEP] Emit OracleReportCreated event
5. [STEP] Anyone calls Diamond.resolveMarket(marketId)
6. [STEP] Diamond reads outcome from oracle, sets isResolved=true
7. [END green] MarketResolved event, users can redeem
```

### C4. Note: ChainlinkOracle (#A7), UMAOracle (#A8), Refund mode (#A9) — **reuse from Group A**

---

## Group D — kinh-te/ (Economics) — 3 SVGs

### D1. Rewards overview flow `→ kinh-te/rewards.md:7`
**File**: `57-rewards-overview.svg`

```
Fan-in diagram — 4 reward types converge to output.

Left: "You trade on PrediX" (blue start node)
4 branches:
- Streak
- Badge
- Daily Challenge
- Reward Box
All 4 → "Convert reward" merge → "PRX / USDC / NFT" (green end)

Vertical TB layout, clean.
```

### D2. Gauge voting flywheel `→ kinh-te/veprx-gauge.md:41`
**File**: `58-gauge-flywheel.svg`

```
Circular flow — gauge voting flywheel loop.

10 nodes in a circle (clockwise):
1. User locks PRX
2. vePRX weight
3. Vote gauge weekly
4. Treasury distributes LP subsidy (by % vote)
5. LP earns more
6. Pool depth increases
7. Trader happier
8. Volume increases
9. Fee increases
10. stkPRX yield increases
→ back to 1 (user buys PRX to stake)

Circular/loop layout, arrows connecting each step.
Highlight nodes 8-10 (Volume, Fee, Yield) with different color.
```

### D3. Lock boost tiers `→ kinh-te/staking-real-yield.md:33`
**File**: `59-lock-boost-tiers.svg`

```
Horizontal tier diagram — staking lock boost levels.

6 horizontal bars stacked vertically (shortest top, longest bottom):
- No lock (stkPRX): 1.0× yield — gray
- Lock 3m: 1.1× — light blue
- Lock 6m: 1.25× — blue
- Lock 12m: 1.5× — blue
- Lock 24m: 2.0× — dark blue
- Lock 48m (max): 2.5× — green (highlighted)

Each bar: label left, multiplier right. Bar length proportional to multiplier.
Clean horizontal bar chart style.
```

---

## Group E — tai-nguyen/ + developers/ — 5 SVGs

### E1. Privacy levels `→ tai-nguyen/settings-i18n.md:90`
**File**: `60-privacy-levels.svg`

```
Decision tree — 4 privacy levels.

Top: "Privacy level" decision
4 branches (left to right, green → red gradient):
- Default: Public profile, positions visible, history visible (green)
- Hide positions: Public profile, positions hidden, history visible
- Hide history: Public profile, history hidden, aggregate only
- Anonymous: Pseudonym + avatar, address hidden, aggregate only (red)

Note below: "On-chain data always public — app settings control UI only"
```

### E2. Developer integration paths `→ developers/README.md:7`
**File**: `61-dev-integration-paths.svg`

```
Decision flow — developer integration choices.

Center: "Goal?" decision diamond
4 branches:
- App users trade PrediX → "Router integration" (link)
- Fetch market/portfolio data → "API reference" (link)
- Bot / mobile / web app → "Bots & mobile" (link)
- Test before mainnet → "Testnet info" (link)

Simple decision fan-out. Color all options same blue.
```

### E3. Testnet develop flow `→ developers/testnet.md:125`
**File**: `62-testnet-dev-flow.svg`

```
Horizontal pipeline — testnet development workflow.

5 stages left to right:
1. "Local dev" (anvil fork) — blue
2. "Test integrate on testnet" — blue
3. "Test edge cases with faucet" — blue
4. "Self-audit, handle errors" — blue
5. "Deploy/connect mainnet" — green (final)

Horizontal pipeline with arrows between stages.
```

### E4. SIWE auth flow `→ developers/api-reference.md:292`
**File**: Already have `14-siwe-auth.svg` — **REUSE**

### E5. Event source of truth `→ developers/api-reference.md:377`
**File**: `63-event-source-truth.svg`

```
Diagram — which events map to which data.

3 source nodes (left):
- Router.Trade (canonical) — green, bold
- Hook_MarketTraded (analytics) — gray
- PositionSplit/Merge/Redeem/Refund — gray

Arrows to target tables (right):
- Router.Trade → protocolStats (totalVolume, totalTrades), trade table, position table
- Hook_MarketTraded → priceSnapshot table (NOT volume)
- Position events → Audit rows (always land)

Note: "Router.Trade is canonical source — volume/trade counts only from here"
```

### E6. API key registration `→ developers/bots-and-mobile.md:17`
**File**: Already have SVG in existing set, but the Mermaid still exists. Check if `bots-and-mobile.md` L17 mermaid was not replaced. If not, prompt:

```
Horizontal flow — API key creation steps.

5 nodes left to right:
1. "Sign in (SIWE)" — blue start
2. "Create API key in Settings"
3. "Choose scope (read-only / trade / full)"
4. "Set rate limit + IP whitelist"
5. "Receive key + secret" — green end

Subtitle: "Secret shown once — save immediately"
```

---

## Summary — SVG files to create

| # | Filename | Used in | Shared? |
|---|---|---|---|
| 31 | fee-distribution-pie | khai-niem/phi.md | |
| 32 | clob-fee-tiers | khai-niem/phi.md | |
| 33 | adaptive-fee-split | khai-niem/phi.md | |
| 34 | clob-3-match-types | khai-niem/clob-va-amm.md | |
| 35 | mev-protection | khai-niem/clob-va-amm.md | |
| 36 | oracle-types | khai-niem/resolution.md + giao-thuc/oracle.md | ✅ shared |
| 37 | chainlink-oracle-flow | khai-niem/resolution.md + giao-thuc/oracle.md | ✅ shared |
| 38 | uma-oracle-flow | khai-niem/resolution.md + giao-thuc/oracle.md | ✅ shared |
| 39 | refund-mode | khai-niem/resolution.md + giao-thuc/oracle.md | ✅ shared |
| 40 | outcome-tokens | khai-niem/outcome-tokens.md (4-in-1) | |
| 41 | chart-sources | huong-dan/chart-va-timeframe.md | |
| 42 | notification-channels | huong-dan/notifications.md | |
| 43 | price-alert-flow | huong-dan/notifications.md | |
| 44 | portfolio-overview | huong-dan/portfolio.md | |
| 45 | pnl-calc | huong-dan/portfolio.md | |
| — | (reuse 28-mindmap-badges) | huong-dan/portfolio.md | ✅ reuse |
| 46 | market-creation-decision | huong-dan/tao-market.md | |
| 47 | discussion-features | huong-dan/discussion.md | |
| 48 | social-feed | huong-dan/discussion.md | |
| 49 | moderation-flow | huong-dan/discussion.md | |
| 50 | trader-profile | huong-dan/leaderboard.md | |
| 51 | copy-trading-flow | huong-dan/leaderboard.md | |
| 52 | lp-risk-reward | huong-dan/cung-cap-thanh-khoan.md | |
| 53 | event-multi-outcome | huong-dan/event-multi-outcome.md (2-in-1) | |
| 54 | diamond-upgrade-flow | giao-thuc/bao-mat.md | |
| 55 | severity-tiers | giao-thuc/bao-mat.md | |
| 56 | manual-oracle-flow | giao-thuc/oracle.md | |
| 57 | rewards-overview | kinh-te/rewards.md | |
| 58 | gauge-flywheel | kinh-te/veprx-gauge.md | |
| 59 | lock-boost-tiers | kinh-te/staking-real-yield.md | |
| 60 | privacy-levels | tai-nguyen/settings-i18n.md | |
| 61 | dev-integration-paths | developers/README.md | |
| 62 | testnet-dev-flow | developers/testnet.md | |
| 63 | event-source-truth | developers/api-reference.md | |

**Total new SVGs to create: 33** (43 mermaid blocks - 4 shared duplicates - 3 reuse existing - 3 combined multi-panel)

**Reuse existing SVGs**:
- `14-siwe-auth.svg` → developers/api-reference.md (duplicate of ket-noi-vi SIWE)
- `28-mindmap-badges.svg` → huong-dan/portfolio.md (same badge mindmap)
- Existing bots-and-mobile SVG if available

**Shared new SVGs** (1 SVG used in 2 files):
- `36-oracle-types.svg` → khai-niem/resolution + giao-thuc/oracle
- `37-chainlink-oracle-flow.svg` → same
- `38-uma-oracle-flow.svg` → same
- `39-refund-mode.svg` → same
