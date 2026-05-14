---
description: >-
  Set your own price and avoid slippage with PrediX Limit Orders. Ideal for
  large trades and market making, featuring partial fills and advanced synthetic
  matching.
---

# Limit order (CLOB)

Place a price and wait for a fill on the **on-chain order book**. You become a **maker** — and enjoy price improvement when matched.

{% hint style="info" %}
**Limit vs Market.** Use **Limit** when you have a target price and are willing to wait for the market to reach it. Use **Market** when you want to enter or exit a position **right now**.
{% endhint %}

## When to use

* ✅ You have a **price view** — e.g., buy YES @ $0.45 while the market is at $0.50, wait for the price to drop
* ✅ **Market making** — place BUY and SELL orders simultaneously, earn the spread
* ✅ **Large trades** where you want to avoid AMM slippage
* ❌ Avoid when you need **immediate execution** — the order may never fill

## How to Place a Limit Order (Buy YES @ $0.45)

{% stepper %}
{% step %}
### <mark style="color:orange;">Step 1: Open the Limit Panel</mark>

* Navigate to the market trading panel and select the Limit tab.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 2: Configure Your Order</mark>

* Side: Select YES.
* Direction: Ensure Buy is selected.
* Price: Enter your target price (e.g., 0.45).
* Amount: Enter the total you wish to spend in USDC (e.g., 100). The UI will automatically calculate the equivalent amount of YES tokens.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 3: Review the Order Preview</mark>

Before finalizing, verify the potential outcome:

* If filled: You will receive approximately 222.22 YES tokens ($$ $100 / 0.45$ $$).
* USDC Locked: 100 USDC will be held in escrow.

{% hint style="info" %}
Note: These funds are released immediately if you cancel the order or when a match occurs.
{% endhint %}
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 4: Execute and Confirm</mark>

* Click Place Order.
* Confirm the transaction in your wallet (Touch ID for passkey or the MetaMask popup for EOA).
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 5: Monitor Your Order</mark>

* Once confirmed, your order is active. You can track, modify, or cancel it under [Portfolio](portfolio.md)→ Open Orders.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
**Maker price improvement.** If a taker fills your order in the same block as you place it, the taker pays your limit price, not the mid. You — the maker — **enjoy a better price than the AMM spot** would give. This is the structural advantage CLOB makers have over AMM swappers.
{% endhint %}

## Tick size

Prices must be multiples of $0.01: `0.01, 0.02, …, 0.99`.

* `0.455` will be rejected or rounded.
* 99 ticks total, stored in a compressed bitmap in the `PrediXExchange` contract.

`0.00` and `1.00` are not supported — a price of certainty has no liquidity.

## 3 match types

When someone places a market order or an opposing limit order, the Exchange matches in one of three ways:

### <mark style="color:orange;">1. Complementary (most common)</mark>

```
You:     BUY YES @ $0.45 — 100 USDC
Other:   SELL YES @ $0.44 — 50 USDC
Match:   Taker sells YES to you @ $0.44 (better than your limit)
         You receive 50 / 0.44 ≈ 113.64 YES
         Remaining 50 USDC stays locked
```

### <mark style="color:orange;">2. Mint (synthetic)</mark>

```
You:     BUY YES @ $0.60
Other:   BUY NO @ $0.45
Total:   $1.05 ≥ $1
Action:  Diamond mints a YES+NO pair from $1 USDC
         YES goes to you, NO goes to the other party
         Spread $0.05 → protocol
```

Both parties receive the token they want; the protocol earns the spread.

### <mark style="color:orange;">3. Merge (synthetic)</mark>

```
You:     SELL YES @ $0.40
Other:   SELL NO @ $0.45
Total:   $0.85 ≤ $1
Action:  Diamond collects YES + NO, burns them → $1 USDC
         Pays $0.40 to you + $0.45 to the other party
         Spread $0.15 → protocol
```

You do not need to calculate any of this — the matching engine optimizes automatically. The UI shows `matchType` in History.

## How to Cancel an Order

{% stepper %}
{% step %}
### <mark style="color:orange;">Step 1: Access Your Open Orders</mark>

* Navigate to your [Portfolio](portfolio.md) section.
* Select the Open Orders tab to view all currently active limit orders.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 2: Initiate Cancellation</mark>

* Locate the specific order you wish to stop.
* Click the Cancel button next to that order.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 3: Confirm and Release Funds</mark>

* Confirm the request in your wallet (e.g., biometric authentication for passkeys or a signature for EOA).
* Once the transaction is signed, your locked USDC or tokens are automatically released back to your wallet in the same transaction.
{% endstep %}
{% endstepper %}

No protocol fee. Gas is paid by the user by default; sponsor coverage applies if the user qualifies for the program (applies to both account types).

## Partial fill

Orders can be filled incrementally:

* BUY YES @ $0.45, 100 USDC → someone takes 40 USDC → 60 USDC remains locked on the book.
* Status: `OPEN` → `PENDING` (per indexer).
* The remainder continues to wait for a match, or you can cancel it.

## Limits

|                                 | Value       |
| ------------------------------- | ----------- |
| Max open orders / user / market | 80          |
| Min size                        | $1 USDC     |
| Tick size                       | $0.01       |
| Stop-loss / OCO                 | TBA Phase 2 |

## Notifications

The app sends notifications when:

* An order **fills** (full or partial)
* An order is **cancelled** (by you or due to market resolution)
* An order is **nearing endTime** (warning to consider cancelling)

For bot / API integrations, see [Developers](../../developers-guide/bots-mobile.md) → Webhooks.

{% hint style="info" %}
#### **Market-making tips**

* **Place orders on both sides** around mid-price → earn the spread as a passive market maker.
* **Tiered orders**: $0.40 50 USDC, $0.42 50 USDC, $0.44 50 USDC → scale in instead of going all-in.
* **Check order book depth** before placing — if the best bid/ask is more than 5% from mid, liquidity is thin and your order may create a new tick.
* **Watch endTime**: Limit orders are automatically cancelled when a market resolves or ends. Reset for each new market.
* **Consider becoming an LP** instead of pure limit orders — LPs have price exposure but earn passive fees on both sides. See [Liquidity provider](../liquidity-and-market/provide-liquidity.md).
{% endhint %}

### Troubleshooting

<details>

<summary><mark style="color:orange;"><strong>"Order rejected — invalid tick size"</strong></mark></summary>

**Reason:** Price has more than 2 decimals or is `0.00` / `1.00`.

**Fix:** Round to the nearest `$0.01` and resubmit. Valid prices are `0.01, 0.02, ..., 0.99`.

</details>

<details>

<summary><mark style="color:orange;"><strong>"Order rejected — insufficient balance"</strong></mark></summary>

**Reason:** Your wallet does not have enough USDC (for BUY) or YES/NO shares (for SELL) to cover the order, factoring in existing open orders.

**Fix:**

* For BUY: Bridge more USDC or cancel an existing open order to free locked funds
* For SELL: check Portfolio for share balance, or use a Market order to acquire shares first

</details>

<details>

<summary><mark style="color:orange;"><strong>"Order rejected — insufficient allowance"</strong></mark></summary>

**Reason:** The Exchange contract is not yet authorized to spend your tokens.

**Fix:** Approve once per token via the prompt that appears on first order. Uses Permit2 where supported (single-signature approval, no separate tx).

</details>

<details>

<summary><mark style="color:orange;"><strong>Order never fills</strong></mark></summary>

**Reason:** Your limit price is too far from current market.

**Fix:**

* Check the orderbook depth — is your price even close to the best bid/ask?
* Re-quote closer to mid (cancel and replace at a tighter price)
* If you need immediate execution, switch to Market order

</details>

<details>

<summary><mark style="color:orange;"><strong>Order disappeared without filling</strong></mark></summary>

**Reason:** Market reached `endTime` — all open orders auto-cancel on resolution start. Locked funds are returned automatically.

**Fix:** Check Portfolio → History for the cancellation event. The market is now in resolution phase — wait for outcome, then redeem if applicable.

</details>

<details>

<summary><mark style="color:orange;"><strong>Partial fill but remainder not matching</strong></mark></summary>

**Reason:** Only part of your order was at a price the taker accepted; rest is too far from mid.

**Fix:** Cancel the remainder and re-place at a better price, or let it sit until the market moves to your level.

</details>

