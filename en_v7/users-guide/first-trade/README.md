---
description: >-
  A step-by-step guide to buying and selling YES/NO positions on PrediX using
  automated CLOB & AMM routing for the best prices.
icon: chart-candlestick
---

# Trading

Buy YES or NO on a market. \~30 seconds from click to confirmation.

#### How to Trade (Buy YES/NO Tokens)

{% stepper %}
{% step %}
### <mark style="color:orange;">Step 1: Find a Market</mark>

* Navigate to the [Markets page](https://app.predix.app/markets).
* Browse the list or use the search bar to find an event you are interested in.
* Click on the market card to open the Detail Page.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 2: Access the Trading Panel</mark>

* On the right-hand side of the page, ensure the Buy tab is selected.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 3: Choose Your Position</mark>

Select your side based on your prediction:

* YES: You believe the event will happen.
* NO: You believe the event will not happen.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 4: Enter Your Investment</mark>

* Input the USDC amount you wish to spend (e.g., $$ $100$ $$).
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 5: Review the Trade Preview</mark>

Before confirming, check the preview details:

<table data-view="cards"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><mark style="color:orange;"><strong>Tokens Received</strong></mark></td><td>The total amount of YES or NO tokens you will get.</td></tr><tr><td><mark style="color:orange;"><strong>Average Price</strong></mark></td><td>The cost per token based on current liquidity.</td></tr><tr><td><mark style="color:orange;"><strong>Slippage Estimate</strong></mark></td><td>The expected price impact of your trade.</td></tr><tr><td><mark style="color:orange;"><strong>Execution Split</strong></mark></td><td>The ratio of the trade filled via CLOB vs. AMM.</td></tr></tbody></table>
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 6: Confirm and Execute</mark>

* Click the Buy button.
* Confirm the request in your wallet:
  * Passkey users: Use Touch ID / Face ID.
  * EOA users: Approve the MetaMask (or other wallet) popup.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 7: Completion</mark>

* The transaction typically confirms in \~2 seconds on Unichain.
* Your new position will immediately appear in your [Portfolio](portfolio.md).
{% endstep %}
{% endstepper %}

## What Happens Under the Hood

![First trade: connect wallet → bridge USDC → select market → buy YES/NO → receive outcome tokens](../../.gitbook/assets/11-first-trade.svg)

All in 1 atomic tx. If slippage exceeds tolerance → revert, no funds lost.

## Order Types

| Type                 | When to use                                    |
| -------------------- | ---------------------------------------------- |
| **Market** (instant) | Enter immediately at current price             |
| **Limit** (CLOB)     | Set a price and wait for it to fill            |
| **Split**            | Mint a YES+NO pair from USDC for market-making |
| **Merge**            | Burn a YES+NO pair to reclaim USDC             |

Details: [Market order](market-order.md), [Limit order](limit-order.md).

## Practical Example

Market: _"BTC above $100k before 2027-01-01?"_. Current YES price = $0.48.

You spend 100 USDC to buy YES:

| Path                         | Amount in    | Avg price  | YES out       |
| ---------------------------- | ------------ | ---------- | ------------- |
| CLOB (existing limit orders) | 40 USDC      | $0.480     | 83.3 YES      |
| AMM swap                     | 60 USDC      | $0.485     | 122.7 YES     |
| **Total**                    | **100 USDC** | **$0.483** | **\~205 YES** |

If BTC exceeds $100k before the deadline:

* Market resolves YES = true.
* You redeem 205 YES → receive USDC. Profit > 100 USDC.

If the event doesn't happen:

* YES tokens = $0. Loss of 100 USDC.

## How to Sell a Position

{% stepper %}
{% step %}
### <mark style="color:orange;">Step 1: Switch to the Sell Tab</mark>

* Open the trading panel on the right side of the market detail page.
* Select the Sell tab.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 2: Select Your Asset</mark>

* Choose the specific tokens you currently hold (YES or NO) that you wish to liquidate.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 3: Enter the Amount</mark>

* Input the quantity of tokens you want to sell.
* You can also use the percentage shortcuts (e.g., 25%, 50%, Max) to quickly fill the amount.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 4: Review and Confirm</mark>

* Preview USDC received: Check the estimated amount of USDC that will be credited to your wallet after the sale.
* Confirm: Click the Sell button and approve the transaction in your wallet to complete the trade.
{% endstep %}
{% endstepper %}

The Router finds the best reverse path — drains CLOB bid orders first, swaps the remainder through AMM.

## Hold Until Resolution

Don't sell — hold tokens until the market resolves, then redeem 1:1 for USDC if you win.&#x20;

Details: [Redeem & refund](redeem-refund.md).

## Common First-Time Errors

* **"Insufficient USDC balance"** — Bridge USDC to Unichain first. See [Bridge](/broken/pages/N6PP2E6mV0vPeJ9TkAjs).
* **"Slippage exceeded"** — Price moved beyond tolerance while the tx was pending. Increase slippage (default 0.5% → 1%) or retry.
* **"Wallet not connected"** — Click Sign in / Connect wallet in the header.
* **"Market paused"** — Rare — admin paused the market for security reasons. Check the notice in the UI.
