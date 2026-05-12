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

## Steps — Buy YES

1. Go to the market detail page. In the right panel, select the **Buy** tab.
2. Choose the **YES** side.
3. Enter the USDC amount to spend (e.g. 100).
4. Set **slippage tolerance** (default 0.5%; increase to 1-2% for low-liquidity markets).
5. The preview displays:
   * Estimated YES received
   * Average price
   * Actual slippage
   * CLOB / AMM split ratio
6. Click **Buy** → confirm in your wallet (Touch ID for passkey, MetaMask popup for EOA).
7. Wait \~2 seconds. The transaction appears in [Portfolio](portfolio.md) → History.

## Sell YES

Same flow but under the **Sell** tab:

1. Select YES, enter the amount to sell.
2. Preview the USDC you will receive.
3. Confirm.

The Router will:

* Drain **bid orders** on the CLOB first (users wanting to buy YES).
* Swap via AMM if CLOB depth is insufficient.
* In some cases, swap through the NO pool + synthetic route (buy NO with fresh USDC → merge with YES → pay out USDC).

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

| Error                    | Reason                             | Fix                                                |
| ------------------------ | ---------------------------------- | -------------------------------------------------- |
| "Slippage exceeded"      | Price moved beyond tolerance       | Increase slippage or retry                         |
| "Insufficient liquidity" | CLOB + AMM lack sufficient depth   | Reduce size or use a [limit order](limit-order.md) |
| "Market paused"          | Admin paused for security reasons  | Check the UI notice                                |
| "Past endTime"           | Trading has closed for this market | Wait for resolution to redeem or get a refund      |
| "Insufficient USDC"      | Wallet lacks USDC                  | [Bridge](bridge.md) or top up                      |

### Troubleshooting

<details>

<summary>"Slippage exceeded"</summary>

**Reason:** Price moved beyond your tolerance between quote and execution (usually because of another trader filling at the same moment, or a stale quote).

**Fix:**

* Increase slippage tolerance to `1–2%` and retry
* Reduce trade size (smaller orders have less price impact)
* Wait a few seconds for the orderbook to refresh, then re-quote

</details>

<details>

<summary>"Insufficient liquidity"</summary>

**Reason:** Combined CLOB + AMM depth is not enough to fill your size.

**Fix:**

* Reduce size and try again
* Use a Limit order instead — rest on the book and wait for fills
* Split the trade into smaller chunks

</details>

<details>

<summary>"Market paused"</summary>

**Reason:** Admin paused trading on this market for security reasons (e.g., oracle issue, suspected manipulation).

**Fix:** Check the UI notice on the market page. Pauses are usually short-term (minutes to hours). Follow the announcement on Discord for updates.

</details>

<details>

<summary>"Past endTime"</summary>

**Reason:** Trading has closed because the market reached its scheduled end time (resolution phase).

**Fix:** Wait for resolution. Once resolved, holders of the winning side can redeem for USDC.

</details>

<details>

<summary>"Insufficient USDC"</summary>

**Reason:** Your Unichain wallet does not have enough USDC for the trade + gas.

**Fix:**

* Bridge USDC from another chain or CEX
* Reduce the trade amount
* Top up via a direct CEX withdrawal to Unichain

</details>

<details>

<summary>Transaction stuck "pending"</summary>

**Reason:** Network congestion or RPC issue. Unichain is normally fast (\~200ms blocks) but transient spikes happen.

**Fix:**

* Wait 30 seconds — most pendings resolve
* Refresh the page; the transaction may have already confirmed
* If stuck >5 minutes, check the explorer (uniscan.xyz) using your tx hash
* As a last resort, "Speed up" or "Cancel" the tx in your wallet

</details>

***
