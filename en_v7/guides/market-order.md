# Market order

Buy or sell instantly at the current price. The Router automatically finds the best path between CLOB and AMM.

## When to use

* Enter a position **immediately**, accepting the market price.
* Small trades (< 1% of liquidity) where slippage is acceptable.
* No strong view on a better price ahead.

Want to wait for a better price? See [Limit order](limit-order.md).

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

![Router waterfall: quote → CLOB fillMarketOrder try → AMM swap fallback → virtual-NO two-pass → cleanup dust → assert balance=0](../.gitbook/assets/15-router-waterfall.svg)

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
| "Insufficient USDC"      | Wallet lacks USDC                  | [Bridge](../getting-started/bridge.md) or top up   |
