# Prediction Markets

A prediction market lets participants trade on the outcome of future events. Token prices reflect the crowd's probability estimate in real time.

## How Prices Reflect Probability

Each market has two tokens:

- **YES token** — pays $1.00 if the event happens
- **NO token** — pays $1.00 if the event does not happen

If YES trades at $0.72, the market implies a **72% probability** the event will occur.

## The $1 Invariant

YES price + NO price ≈ $1.00, always. If this relationship breaks, arbitrageurs profit by:

- **YES + NO > $1:** Merge tokens into USDC for risk-free profit
- **YES + NO < $1:** Split USDC into tokens, sell both for more than $1

This mechanism keeps PrediX prices efficient. See [Split & Merge](split-and-merge.md) for details.

## How PrediX Differs

| Platform | Type | Composability | Liquidity |
|----------|------|---------------|-----------|
| Polymarket | CLOB only | ERC-1155 (non-composable) | Deep but single-venue |
| Augur/Omen | AMM only | Limited | Thin, full-range |
| **PrediX** | **CLOB + AMM** | **ERC-20 (full DeFi)** | **Hybrid, concentrated** |

PrediX outcome tokens are standard ERC-20 — they can be LP'd on Uniswap, used as collateral, or integrated into any DeFi protocol. No other major prediction market offers this.

## Next Steps

- [Outcome Tokens](outcome-tokens.md) — ERC-20 token mechanics
- [Split & Merge](split-and-merge.md) — the price anchor mechanism
- [Fees](fees.md) — dynamic fee schedule
