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

## Steps — place a buy YES @ $0.45

1. In the market panel, select the **Limit** tab.
2. Side **YES**, direction **Buy**.
3. Price: `0.45`.
4. Amount: `100 USDC` (the UI converts to YES).
5. Preview:
   * If filled: receive `100 / 0.45 ≈ 222.22 YES`.
   * USDC locked: 100 USDC (released on cancel or match).
6. Click **Place Order** → confirm in your wallet.
7. The order appears in [Portfolio](portfolio.md) → **Open Orders**.

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

### 1. Complementary (most common)

```
You:     BUY YES @ $0.45 — 100 USDC
Other:   SELL YES @ $0.44 — 50 USDC
Match:   Taker sells YES to you @ $0.44 (better than your limit)
         You receive 50 / 0.44 ≈ 113.64 YES
         Remaining 50 USDC stays locked
```

### 2. Mint (synthetic)

```
You:     BUY YES @ $0.60
Other:   BUY NO @ $0.45
Total:   $1.05 ≥ $1
Action:  Diamond mints a YES+NO pair from $1 USDC
         YES goes to you, NO goes to the other party
         Spread $0.05 → protocol
```

Both parties receive the token they want; the protocol earns the spread.

### 3. Merge (synthetic)

```
You:     SELL YES @ $0.40
Other:   SELL NO @ $0.45
Total:   $0.85 ≤ $1
Action:  Diamond collects YES + NO, burns them → $1 USDC
         Pays $0.40 to you + $0.45 to the other party
         Spread $0.15 → protocol
```

You do not need to calculate any of this — the matching engine optimizes automatically. The UI shows `matchType` in History.

## Cancel an order

1. [Portfolio](portfolio.md) → **Open Orders**.
2. Find the order, click **Cancel**.
3. Confirm in your wallet → locked USDC / tokens are released to your wallet in the same transaction.

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

For bot / API integrations, see Developers → Webhooks.

## Market-making tips

* **Place orders on both sides** around mid-price → earn the spread as a passive market maker.
* **Tiered orders**: $0.40 50 USDC, $0.42 50 USDC, $0.44 50 USDC → scale in instead of going all-in.
* **Check order book depth** before placing — if the best bid/ask is more than 5% from mid, liquidity is thin and your order may create a new tick.
* **Watch endTime**: Limit orders are automatically cancelled when a market resolves or ends. Reset for each new market.
* **Consider becoming an LP** instead of pure limit orders — LPs have price exposure but earn passive fees on both sides. See [Liquidity provider](../liquidity-and-market/provide-liquidity.md).

## Maker vs taker — why makers get a better price

If your order matches immediately in the same block as a taker placing the opposite side:

* The taker fills at exactly your limit price, not the mid.
* You (the maker) enjoy **price improvement** — a better price than the AMM spot.

This is why CLOB makers typically get better prices than standard AMM swaps.

## Watch your orders

* The app sends notifications when:
  * An order fills (full or partial)
  * An order is cancelled (by you or due to market resolution)
  * An order is nearing endTime (warning to consider cancelling)
* Webhook / API integration for bots — see [Developers](../../developers-guide/bots-mobile.md).

### Troubleshooting

<details>

<summary>"Order rejected — invalid tick size"</summary>

**Reason:** Price has more than 2 decimals or is `0.00` / `1.00`.

**Fix:** Round to the nearest `$0.01` and resubmit. Valid prices are `0.01, 0.02, ..., 0.99`.

</details>

<details>

<summary>"Order rejected — insufficient balance"</summary>

**Reason:** Your wallet does not have enough USDC (for BUY) or YES/NO shares (for SELL) to cover the order, factoring in existing open orders.

**Fix:**

* For BUY: Bridge more USDC or cancel an existing open order to free locked funds
* For SELL: check Portfolio for share balance, or use a Market order to acquire shares first

</details>

<details>

<summary>"Order rejected — insufficient allowance"</summary>

**Reason:** The Exchange contract is not yet authorized to spend your tokens.

**Fix:** Approve once per token via the prompt that appears on first order. Uses Permit2 where supported (single-signature approval, no separate tx).

</details>

<details>

<summary>Order never fills</summary>

**Reason:** Your limit price is too far from current market.

**Fix:**

* Check the orderbook depth — is your price even close to the best bid/ask?
* Re-quote closer to mid (cancel and replace at a tighter price)
* If you need immediate execution, switch to Market order

</details>

<details>

<summary>Order disappeared without filling</summary>

**Reason:** Market reached `endTime` — all open orders auto-cancel on resolution start. Locked funds are returned automatically.

**Fix:** Check Portfolio → History for the cancellation event. The market is now in resolution phase — wait for outcome, then redeem if applicable.

</details>

<details>

<summary>Partial fill but remainder not matching</summary>

**Reason:** Only part of your order was at a price the taker accepted; rest is too far from mid.

**Fix:** Cancel the remainder and re-place at a better price, or let it sit until the market moves to your level.

</details>

