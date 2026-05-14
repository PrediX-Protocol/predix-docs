---
description: >-
  A guide to PrediX Market Orders: instant execution, optimized CLOB & AMM
  routing, and automated slippage control.
---

# Market order

Buy or sell instantly at the current market price. The **Router** automatically finds the best execution path between the CLOB (limit order book) and the AMM (Uniswap v4 pool).

{% hint style="info" %}
**Market vs Limit.** Use **Market** when you want to enter/exit a position **now** and are okay with the current price. Use **Limit** when you have a target price and are willing to wait.
{% endhint %}

## When to use

* ✅ Enter a position **immediately**, accepting the prevailing market price
* ✅ Small trades (< 1% of liquidity) where slippage is acceptable
* ✅ You have no strong view on a better price ahead
* ❌ Avoid for large size in thin markets — use a limit order instead

Want to wait for a better price? See [Limit order](limit-order.md).

## Buy / Sell Semantics

| Action            | What you specify                          | What you receive                   |
| ----------------- | ----------------------------------------- | ---------------------------------- |
| **Buy YES / NO**  | USDC amount to spend (e.g. `100 USDC`)    | Variable number of YES / NO shares |
| **Sell YES / NO** | Number of shares to sell (e.g. `200 YES`) | Variable USDC amount               |

{% hint style="warning" %}
**Asymmetric input.** Buy is denominated in **USDC**, Sell is denominated in **shares**. The UI labels each field correctly — but if you integrate via API, be sure to use `amountIn` (USDC for buy) vs `sharesIn` (shares for sell).
{% endhint %}

***

## How to Buy YES

{% stepper %}
{% step %}
### <mark style="color:orange;">Step 1: Access the Trading Panel</mark>

* Navigate to the Market Detail Page of your chosen event.
* On the right-hand panel, ensure the Buy tab is selected.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 2: Select Your Side & Amount</mark>

* Choose the YES side (indicating you believe the event will happen).
* Enter the USDC amount you wish to spend (e.g., $$ $100$ $$).
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 3: Adjust Slippage Tolerance</mark>

* Set your preferred slippage tolerance.

{% hint style="info" %}
Tip: While the default is 0.5%, you may want to increase this to 1-2% when trading in low-liquidity markets to ensure your order fills.
{% endhint %}
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 4: Review the Trade Preview</mark>

Before clicking buy, verify the following details:

* [x] Estimated YES received: The total tokens you will get.
* [x] Average price: The projected cost per token.
* [x] Actual slippage: The expected price impact based on current liquidity.
* [x] CLOB / AMM split ratio: How your trade is distributed across the Order Book and Liquidity Pool.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 5: Execute and Confirm</mark>

* Click the Buy button.
* Confirm the transaction in your wallet:
  * Passkey users: Authenticate via Touch ID or Face ID.
  * EOA users: Approve the MetaMask or wallet popup.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 6: Success</mark>

* Wait approximately 2 seconds for the transaction to process.
* Once confirmed, you can view the trade in your [Portfolio](portfolio.md) under the History tab.
{% endstep %}
{% endstepper %}

## How to Sell YES

Same flow but under the **Sell** tab:

{% stepper %}
{% step %}
### <mark style="color:orange;">Step 1: Switch to the Sell Tab</mark>

* Navigate to the trading panel on the market detail page.
* Select the Sell tab.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 2: Configure Your Sale</mark>

* Ensure YES is selected.
* Enter the amount of tokens you wish to sell.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 3: Review and Confirm</mark>

* Preview: Check the estimated USDC you will receive after fees and slippage.
* Confirm: Click the Sell button and approve the transaction in your wallet.
{% endstep %}
{% endstepper %}

#### <mark style="color:orange;">How the Execution Router Works</mark>

When you sell, the PrediX Router automatically finds the most efficient path to give you the best price by following this hierarchy:

1. CLOB Liquidity: It first "drains" existing bid orders on the Central Limit Order Book (matching you with users waiting to buy YES).
2. AMM Swap: If the order book depth is insufficient, it swaps the remaining tokens through the Automated Market Maker (Liquidity Pool).
3. Synthetic Routing: In specific scenarios, the router uses a synthetic route to maximize your return:
   * It buys NO tokens using fresh USDC.
   * It merges those NO tokens with your YES tokens (which equals 1 unit of the underlying collateral).
   * It pays out the resulting USDC to you.

## Buy / sell NO

Symmetric to YES. The Router may use the **virtual-NO trick** when the NO-USDC pool lacks liquidity:

![Router waterfall: quote → CLOB fillMarketOrder try → AMM swap fallback → virtual-NO two-pass → cleanup dust → assert balance=0](../../.gitbook/assets/15-router-waterfall.svg)

You do not need to worry about the details — the UI simply shows "Buy NO" and the final amount.

## Slippage

Slippage is the difference between the previewed price and the actual execution price.

* **Default 0.5%**: Suitable for liquid markets.
* **1-2%**: Increase for markets with a wide spread.
* **> 5%**: Rarely advisable — the Router will warn you.

If slippage is exceeded the transaction **reverts** and no funds are lost (only gas is consumed — sponsor coverage applies if the user qualifies for the program, regardless of account type).

## Split / merge shortcut

In the same panel, under the **Split** / **Merge** tabs:

* **Split**: 100 USDC → 100 YES + 100 NO. Useful when you want to hold both sides and sell them separately (market making).
* **Merge**: 100 YES + 100 NO → 100 USDC. Useful when you hold both sides and want to withdraw.

No protocol fee. Gas is paid by the user by default; sponsor coverage applies if the user qualifies for the program (applies to both account types).

## Common errors

| Error                    | Reason                             | Fix                                                    |
| ------------------------ | ---------------------------------- | ------------------------------------------------------ |
| "Slippage exceeded"      | Price moved beyond tolerance       | Increase slippage or retry                             |
| "Insufficient liquidity" | CLOB + AMM lack sufficient depth   | Reduce size or use a [limit order](limit-order.md)     |
| "Market paused"          | Admin paused for security reasons  | Check the UI notice                                    |
| "Past endTime"           | Trading has closed for this market | Wait for resolution to redeem or get a refund          |
| "Insufficient USDC"      | Wallet lacks USDC                  | [Bridge](/broken/pages/N6PP2E6mV0vPeJ9TkAjs) or top up |
