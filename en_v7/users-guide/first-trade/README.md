# Trading

Buy YES or NO on a market. \~30 seconds from click to confirmation.

## Steps

1. Go to [Markets](https://app.predix.app/markets), browse or search for a market you're interested in.
2. Click on the card → detail page.
3. Right panel: select the **Buy** tab.
4. Choose side: **YES** (event will happen) or **NO** (event won't happen).
5. Enter the USDC amount to spend (e.g., 100).
6. Preview shows:
   * Amount of YES / NO tokens received
   * Average price
   * Slippage estimate
   * CLOB / AMM split ratio
7. Click **Buy** → wallet requests confirmation (Touch ID for passkey, MetaMask popup for EOA).
8. Tx confirms in \~2 seconds on Unichain. Position appears in [Portfolio](portfolio.md).

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

## Sell a Position

Same panel, **Sell** tab:

1. Select the YES or NO tokens you hold.
2. Enter the amount you want to sell.
3. Preview USDC received.
4. Confirm.

The Router finds the best reverse path — drains CLOB bid orders first, swaps the remainder through AMM.

## Hold Until Resolution

Don't sell — hold tokens until the market resolves, then redeem 1:1 for USDC if you win. Details: [Redeem & refund](redeem-refund.md).

## Common First-Time Errors

* **"Insufficient USDC balance"** — Bridge USDC to Unichain first. See [Bridge](bridge.md).
* **"Slippage exceeded"** — Price moved beyond tolerance while the tx was pending. Increase slippage (default 0.5% → 1%) or retry.
* **"Wallet not connected"** — Click Sign in / Connect wallet in the header.
* **"Market paused"** — Rare — admin paused the market for security reasons. Check the notice in the UI.
