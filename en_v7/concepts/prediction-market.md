# Prediction Market

A market for trading tokens that reflect the **probability** of a future event occurring. Prices update in realtime based on supply and demand.

## Example

Event: *"Bitcoin above $100,000 before 2027-01-01?"*

Current market pricing:
- **YES** = `$0.68` → the market estimates a **68% probability** the event will happen
- **NO** = `$0.32` → 32% probability it won't happen
- Total YES + NO ≈ `$1.00` (see [Outcome tokens](outcome-tokens.md) for why)

Payout matrix when the market resolves:

| Actual outcome | YES holder | NO holder |
|---|---|---|
| Event **happens** | `1 YES = $1.00` ✅ | `1 NO = $0` ❌ |
| Event **doesn't happen** | `1 YES = $0` ❌ | `1 NO = $1.00` ✅ |

## Why Price Is a Good Information Signal

- People who believe the event will happen → buy YES → push the price up.
- Aggregates opinions from many participants into a single transparent number.
- **Skin in the game**: real money → traders only profit if they're correct. Stronger than ordinary polls (where respondents have nothing at stake).
- Research consistently shows that prediction markets are often more accurate than expert forecasts for events with statistical data (elections, sports, macroeconomics).

## Compared to Traditional Bookmakers

| | Bookmaker (Bet365, 1xBet...) | Prediction market |
|---|---|---|
| Counterparty | House (the platform) | Other users (peer-to-peer) |
| Pricing | House sets it, wide spread | Market-driven, AMM + CLOB |
| Custody | House holds funds (custodial) | Non-custodial, on-chain |
| Sell before resolution | Difficult / impossible | Yes — resell tokens anytime |
| Auditable | No | Yes — on-chain explorer |
| Fees | Baked into odds (hidden) | Transparent, shown before confirmation |
| Censorship | House can ban users | Permissionless |

## Market Types on PrediX

| Kind | Description | Example |
|---|---|---|
| **Binary** | Simple YES / NO | "BTC > $100k before 2027?" |
| **Scalar** | Long / short with a strike, linear payout | "Vietnam GDP 2026 (USD billion)?" — long > strike, short < strike |
| **Multi-outcome event** | N sub-markets mutually exclusive, exactly 1 wins | "Who wins FIFA WC 2026?" — 48 teams, 1 market per team |
| **Sports** | Pre-structured for tournaments | Premier League season winner |
| **Grouped** | Markets grouped by theme | "AI capabilities milestones 2026" group |

Multi-outcome details: [Multi-outcome events](../guides/multi-outcome-event.md).

## Limitations of Prediction Markets

- **Oracle dependence**: Requires a source to report the outcome on-chain. Oracles can be wrong, disputed, or delayed. PrediX uses pluggable oracles to mitigate single point of failure risk. See [Resolution](resolution.md).
- **Liquidity**: Markets with no trading activity → wide spread → high slippage. Liquidity providers are incentivized via [gauge voting](../economics/veprx-gauge.md).
- **Tokens only have value within their market context**: 1 YES from market A cannot be used in market B. After resolution, losing tokens = $0.
- **Black swan events**: Extremely rare events may not be priced correctly. Markets self-correct over time as information becomes public.
