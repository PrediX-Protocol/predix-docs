# Provide liquidity (LP)

Provide liquidity to the AMM pool of a market. Earn fees from every swap through that pool.

## Overview

* The YES-USDC pool (and optionally NO-USDC) is a standard Uniswap v4 pool.
* You deposit a pair of tokens into a specific price range → receive an **LP NFT** (Uniswap v4 PositionManager).
* For each swap through the pool, you earn fees pro-rata based on your share.
* You can remove liquidity at any time (except after a market resolves and the pool closes).

## Risk vs reward

![LP risk/reward: earn fee per swap (positive) vs impermanent loss (risk). After resolve: YES wins=$1 or NO wins=$0. Net PnL = final - initial + fees](../../.gitbook/assets/52-lp-risk-reward.svg)

Being an LP is a **directional bet** — you lose if the market resolves toward the side you did not expect. Make sure you understand impermanent loss (IL) and outcome risk before providing liquidity.

## Steps — provide liquidity

1. Go to the market detail page. Select the **Liquidity** tab.
2. Choose a pool: **YES-USDC** or **NO-USDC** (if both are available).
3. Select a price range:
   * **Full range**: $0.01 - $0.99. Safest option, lower fee earnings.
   * **Concentrated**: e.g. $0.40 - $0.60. Higher earnings, higher IL risk if the price moves out of range.
4. Enter the USDC amount + YES amount (the UI auto-balances based on the current price).
5. If you lack YES: the app suggests **Split USDC → YES + NO** (minting both from USDC), and auto-fills the amount.
6. Preview: total deposit, expected APR (based on historical volume), price range.
7. Click **Add Liquidity** → confirm.
8. An LP NFT is received in your wallet. The position appears in the **Liquidity** tab of your portfolio.

## Steps — claim fees

Fees accrue automatically in your position:

1. Portfolio → **Liquidity** tab.
2. The position card shows uncollected fees (USDC + YES).
3. Click **Collect** → fees are claimed to your wallet. No protocol fee. Gas is paid by the user by default; sponsor coverage applies if the user qualifies for the program (applies to both account types).

You can compound by re-depositing the claimed fees into the pool to increase your position.

## Steps — remove liquidity

1. Portfolio → Liquidity → select a position.
2. Click **Remove**.
3. Choose the percentage to withdraw (25% / 50% / 100%).
4. Preview the USDC + YES you will receive.
5. Confirm. Tokens are returned to your wallet in the same transaction; the LP NFT is burned (or its liquidity is reduced for partial withdrawals).

## After market resolution

The pool closes — no more trading or adding liquidity.

![LP after resolve: Hook blocks pool → LP removes liquidity → receives USDC + YES → redeem YES=$1 → total USDC to wallet](../../.gitbook/assets/19-lp-after-resolve.svg)

You can:

* **Remove liquidity** to retrieve USDC + any remaining outcome tokens.
* **Redeem** the winning outcome token → 1 USDC per token.
* The losing token = $0.

## Impermanent loss (IL) in prediction markets

Unlike standard AMM pairs (ETH/USDC), outcome token prices are bounded between $0.01 and $0.99. IL follows a distinct pattern:

```
YES-USDC pool created when YES = $0.50:
- Deposit: 100 USDC + 200 YES = total $200 (200 YES × $0.50 + 100 USDC)
- Suppose YES → $0.80 (new information leads the market to believe the event will occur)
- AMM rebalances: fewer YES, more USDC (constant product k)
- After rebalance: e.g. 150 USDC + 125 YES
- Total = 150 + 125 × 0.80 = 150 + 100 = $250

If you had held instead of LP:
- Held 100 USDC + 200 YES = 100 + 160 = $260

IL = $260 - $250 = $10 (3.85% vs hold)
```

IL is offset by earned fees. If volume is high enough → fees > IL → net profit.

## LP strategies

### Concentrated narrow

Set a tight range ($0.40-$0.60) when you believe the price will fluctuate within that range. Highest earnings when the price sits in the middle of the range. Risk: if the price moves out of range, the position becomes entirely one token and earns no fees until the price returns.

### Concentrated wide

Range $0.20-$0.80. Safer, moderate earnings.

### Full range

$0.01-$0.99. Safest, lowest earnings. Best suited for passive LPs.

### Single-sided LP

Deposit only USDC into a range above the current YES price. When the price rises into your range, USDC converts to YES. This tactic functions like a "scale buy".

## Boost from gauge voting

LPs can receive **subsidies** from the treasury via [gauge voting](../../economics/veprx-gauge.md):

1. vePRX holders vote on which pools receive subsidies.
2. The treasury distributes subsidies proportional to vote share.
3. Pools with more votes → LPs earn fees + PRX subsidies → higher APR.

Track gauge rankings in **Liquidity** → **Gauge** tab.

## Tax / accounting

LP fees are collected in USDC + outcome tokens. Each claim event counts as income (for tax purposes, depending on jurisdiction). Export CSV from your portfolio.

## API integration

LP positions are accessible via:

* Indexer: `GET /api/users/:address/lp-positions`
* BE: `GET /api/v2/users/:address/lp-positions`

Details: [Indexer API](../../developers-guide/api-reference.md#indexer-endpoints).
