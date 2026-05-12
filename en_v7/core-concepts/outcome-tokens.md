# Outcome token (YES/NO)

Each market has 2 ERC-20 tokens. Understanding them = understanding the core mechanism of PrediX.

## Characteristics

* Both are **standard ERC-20**, 6 decimals (same as USDC).
* Each market has its own YES/NO pair — YES tokens from market A cannot be used in market B.
* Only the Diamond contract can mint them (`onlyFactory`).

## Core Invariant — $1 Invariant

```
YES.totalSupply == NO.totalSupply == market.totalCollateral
```

Total YES supply = Total NO supply = USDC locked in the market. **Always**.

![Outcome token $1 invariant: 100 USDC → Diamond mints 100 YES + 100 NO. Split/merge/redeem maintains YES.supply == NO.supply == totalCollateral at all times](../.gitbook/assets/40-outcome-tokens.svg)

This guarantees: **when a market resolves, the protocol always has enough USDC to pay holders of the winning token**. Insolvency is impossible.

## Split — Mint YES + NO from USDC

```
You  ──deposit 100 USDC──▶  Diamond  ──mint 100 YES──▶  You
                                     ──mint 100 NO───▶  You
```

* Atomic: all 3 steps in 1 tx.
* No protocol fee. Gas is paid by the user by default; covered by sponsorship if eligible (applies to both account types).

**When to use**:

* Market-making: sell YES and NO separately at prices > $0.50 each (average price > $1, capture the spread).
* Arbitrage when YES + NO > $1 on the market.

## Merge — Burn YES + NO → USDC

```
You  ──burn 100 YES + 100 NO──▶  Diamond  ──payout 100 USDC──▶  You
```

The reverse of split. Requires holding equal amounts of both tokens.

**When to use**:

* You hold both YES and NO (from trading) and want to withdraw.
* Arbitrage when YES + NO < $1.

## Why YES + NO Always ≈ $1

Because split / merge create **riskless arbitrage**:

* **YES + NO > $1**: Split 1 USDC → 1 YES + 1 NO → sell both for a combined total > $1 = profit. Many do this → price gets pushed down to \~$1.
* **YES + NO < $1**: Buy 1 YES + 1 NO for < $1 → merge → 1 USDC = profit. Many do this → price gets pushed up to \~$1.

Arbitrage is automated by AMM bots, requiring no manual intervention.

## Redeem — After Market Resolution

* Market resolves, e.g., outcome = YES.
* User holding YES → calls `redeem(marketId)` → exchanges YES for USDC.
* NO tokens → $0, cannot be redeemed or traded.

## Refund — When a Market Cannot Be Resolved

If the oracle fails (down, dispute hung), admin enables **refund mode** via a 48h timelock:

* Users burn YES + NO pairs → receive USDC pro-rata.
* Burns at `min(yesBalance, noBalance)` — only the paired portion can be refunded.

Details: [Resolution](resolution.md), [Redeem & refund](../users-guide/first-trade/redeem-refund.md).

## Composable with DeFi

Since they are standard ERC-20, YES / NO tokens can:

* Be added to other Uniswap pools (e.g., YES vs a custom token).
* Be used as lending collateral (when lending protocols list YES/NO).
* Serve as NFT mint conditions (mint when holding YES from a resolved-true market).
* Power vault strategies (e.g., auto-merge when spread arbitrage is available).

ERC-20 standard → compatible with every DeFi protocol out of the box, no custom integration needed.

## Encoding Details

* Amount of 1 USDC = `1_000_000` (6 decimals base unit).
* Amount of 1 YES = `1_000_000` (also 6 decimals).
* Price $0.48 = `480_000` (fixed-point 6 decimals). Valid range: `10_000` to `990_000` (0.01 to 0.99).

Integration details: [developers/router-integration.md](../developers-guide/router-integration.md).
