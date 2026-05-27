---
description: Set your own price and avoid slippage with PrediX Limit Orders.
icon: temperature-list
---

# Limit Order (CLOB)

Place a price and wait for a fill on the **On-chain Order Book**. You become a **maker** - and enjoy price improvement when matched.

### When to use

* ✅ You have a **price view** - e.g., buy YES @ $0.45 while the market is at $0.50, wait for the price to drop
* ✅ **Market making** - place BUY and SELL orders simultaneously, earn the spread
* ✅ **Large trades** where you want to avoid AMM slippage
* ❌ Avoid when you need **immediate execution** - the order may never fill

{% hint style="info" %}
**Limit vs Market.** Use **Limit** when you have a target price and are willing to wait for the market to reach it. Use **Market** when you want to enter or exit a position **right now**.
{% endhint %}

***

### How to Place a Limit Order

<mark style="color:yellow;background-color:yellow;">**\[insert video]**</mark>

{% stepper %}
{% step %}
<mark style="color:$warning;">**Step 1: Open the Limit Panel**</mark>

* Navigate to the market trading panel and select the **Limit** tab.
{% endstep %}

{% step %}
<mark style="color:$warning;">**Step 2: Configure Your Order**</mark>

* Side: Select YES.
* Direction: Ensure Buy is selected.
* Price: Enter your target price (_e.g., buy YES @ $0.45_).
* Amount: Enter the total you wish to spend in USDC. PrediX will automatically calculate the equivalent amount of YES tokens (e.g., _100 USDC_).
{% endstep %}

{% step %}
<mark style="color:$warning;">**Step 3: Preview your Order**</mark>

Before finalizing, verify the potential outcome:

* If filled: You will receive approximately **222.22 YES tokens (**$$ $100 / 0.45$ $$**).**
* USDC Locked: 100 USDC will be held in escrow.

{% hint style="info" %}
Note: These funds are released immediately if you cancel the order or when a match occurs.
{% endhint %}
{% endstep %}

{% step %}
<mark style="color:$warning;">**Step 4: Execute and Confirm**</mark>

* Click Place Order.
* Confirm the request in your wallet:
  * [Passkey Wallet](../wallet-setup/connect-wallet.md#method-1-passkey--smart-account): Use Touch ID / Face ID.
  * [EOA Wallet](../wallet-setup/connect-wallet.md#method-2-crypto-wallet-eoa): Approve the MetaMask (or other wallet) pop
{% endstep %}

{% step %}
<mark style="color:$warning;">**Step 5: Monitor Your Order**</mark>

* Wait approximately 2 seconds for the transaction to process.
* Once confirmed, your order is active. You can view the trade in your [Portfolio](../portfolio.md) under the History tab.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
**Maker price improvement**

* If a taker fills your order in the same block as you place it, the taker pays your limit price, not the mid. You - the maker - **enjoy a better price than the AMM spot** would give.
* This is the structural advantage CLOB makers have over AMM swappers.
{% endhint %}

***

### 3 match types

When someone places a market order or an opposing limit order, PrediX matches in one of three ways:

<details>

<summary><mark style="color:$primary;"><strong>1. Complementary (most common)</strong></mark></summary>

```
You:     BUY YES @ $0.45 - 100 USDC
Other:   SELL YES @ $0.44 - 50 USDC
Match:   Taker sells YES to you @ $0.44 (better than your limit)
         You receive 50 / 0.44 ≈ 113.64 YES
         Remaining 50 USDC stays locked
```

</details>

<details>

<summary><mark style="color:$primary;"><strong>2. Mint (synthetic)</strong></mark></summary>

```
You:     BUY YES @ $0.60
Other:   BUY NO @ $0.45
Total:   $1.05 >= $1
Action:  Diamond mints a YES+NO pair from $1 USDC
         YES goes to you, NO goes to the other party
         Spread $0.05 -> protocol
```

_Both parties receive the token they want; the protocol earns the spread._

</details>

<details>

<summary><mark style="color:$primary;"><strong>3. Merge (synthetic)</strong></mark></summary>

```
You:     SELL YES @ $0.40
Other:   SELL NO @ $0.45
Total:   $0.85 <= $1
Action:  Diamond collects YES + NO, burns them -> $1 USDC
         Pays $0.40 to you + $0.45 to the other party
         Spread $0.15 -> protocol
```

_You do not need to calculate any of this - the matching engine optimizes automatically. The UI shows `matchType` in History._

</details>

***

### Cancel an Order

<mark style="color:yellow;background-color:yellow;">**\[insert video]**</mark>

{% stepper %}
{% step %}
<mark style="color:$warning;">**Step 1: Access Your Open Orders**</mark>

* Navigate to your [Portfolio](../portfolio.md) section.
* Select the Open Orders tab to view all currently active limit orders.
{% endstep %}

{% step %}
<mark style="color:$warning;">**Step 2: Initiate Cancellation**</mark>

* Locate the specific order you wish to stop.
* Click the Cancel button next to that order.
{% endstep %}

{% step %}
<mark style="color:$warning;">**Step 3: Confirm and Release Funds**</mark>

* Confirm the request in your wallet (e.g., biometric authentication for passkeys or a signature for EOA).
* Once the transaction is signed, your locked USDC or tokens are automatically released back to your wallet in the same transaction.
{% endstep %}
{% endstepper %}

{% hint style="info" %}
No protocol fee. Gas is paid by the user by default; sponsor coverage applies if the user qualifies for the program (applies to both account types).
{% endhint %}

***

### Partial fill

Orders can be filled incrementally:

* BUY YES @ $0.45, 100 USDC -> someone takes 40 USDC -> 60 USDC remains locked on the book.
* Status: `OPEN` -> `PENDING` (per indexer).
* The remainder continues to wait for a match, or you can cancel it.

***

### Limits

|                                 | Value         |
| ------------------------------- | ------------- |
| Max open orders / user / market | 50            |
| Min size                        | $1 USDC       |
| Tick size                       | $0.01         |
| Stop-loss / OCO                 | TBA - Phase 2 |

> **Prices must be multiples of $0.01: `0.01, 0.02, ..., 0.99`.**
>
> * `0.455` will be rejected or rounded.
> * 99 ticks total, stored in a compressed bitmap in the `PrediXExchange` contract.
> * `0.00` and `1.00` are not supported - a price of certainty has no liquidity.

***

{% hint style="info" %}
**Market-making tips**

* **Place orders on both sides** around mid-price -> earn the spread as a passive market maker.
* **Tiered orders**: $0.40 50 USDC, $0.42 50 USDC, $0.44 50 USDC -> scale in instead of going all-in.
* **Check order book depth** before placing - if the best bid/ask is more than 5% from mid, liquidity is thin and your order may create a new tick.
* **Watch endTime**: Limit orders are automatically cancelled when a market resolves or ends. Reset for each new market.
* **Consider becoming an LP** instead of pure limit orders - LPs have price exposure but earn passive fees on both sides. See [Liquidity provider](../liquidity-and-market/provide-liquidity.md).
{% endhint %}
