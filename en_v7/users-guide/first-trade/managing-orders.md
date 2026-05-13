# Managing Orders

View, cancel, and track your open limit orders.

### Overview

After placing a limit order, you can monitor it, cancel it, or wait for it to fill. PrediX gives you full control over your resting orders — single cancel, batch cancel, and automatic cleanup when markets resolve.

### Viewing Open Orders

Navigate to **Portfolio → Orders** to view all your active orders across every market. Each row shows:

| Field         | Description                                |
| ------------- | ------------------------------------------ |
| **Market**    | The market the order is placed on          |
| **Side**      | YES or NO                                  |
| **Direction** | Buy or Sell                                |
| **Price**     | Your limit price (e.g., `$0.45`)           |
| **Size**      | Original order size                        |
| **Filled**    | Amount already matched (for partial fills) |
| **Remaining** | Size still resting on the book             |
| **Status**    | `OPEN`, `PARTIAL`, or `PENDING`            |
| **Placed at** | Timestamp                                  |
| **Action**    | Cancel button                              |

You can also view orders for a specific market on the market detail page, under the **Orderbook** tab → **My Orders**.

### Cancelling Orders

#### Cancel a Single Order

To cancel one order:

1. Open Portfolio → Orders or the market's My Orders tab
2. Click the **Cancel** button next to the order
3. Confirm in your wallet
4. Locked USDC or tokens are returned **in the same transaction**

{% hint style="info" %}
**No protocol fee** on cancel. Gas is paid by the user; paymaster sponsorship applies if you qualify (both Passkey and EOA wallets eligible).
{% endhint %}

#### Cancel Multiple Orders

Select multiple orders with checkboxes, then click **Cancel Selected**:

| Wallet                      | Behavior                                                                           |
| --------------------------- | ---------------------------------------------------------------------------------- |
| **Passkey + Smart Account** | All cancels bundled into 1 UserOp — single confirmation, single gas (or sponsored) |
| **Crypto Wallet (EOA)**     | One transaction per order — N confirmations                                        |

#### Cancel All Orders

Click **Cancel All** at the top of the Orders tab to cancel **every open order across every market** in one click.

| Wallet      | Behavior                                |
| ----------- | --------------------------------------- |
| **Passkey** | 1 UserOp bundles all cancels atomically |
| **EOA**     | Sequential transactions (one per order) |

{% hint style="warning" %}
**Cancel All is irreversible.** Once confirmed, all your resting limit orders are gone. If you change your mind, you'll need to re-place each order manually.
{% endhint %}

#### Programmatic Cancel (API)

For bots and automated strategies, the same cancel functions are available via API:

* Cancel a single order
* Cancel orders in batch
* Cancel all orders in a market

### Editing Orders

PrediX **does not support direct order editing**. To change an order's price or size, you must:

1. **Cancel** the existing order
2. **Place a new order** with the updated parameters

For bots that frequently update quotes, the API supports `cancelAndReplace` — a single atomic operation that cancels and re-places in one transaction. See API → Replace Order.

### Partial Fills

A limit order can be filled incrementally as different takers hit it:

```
BUY YES @ $0.45, 100 USDC
  → taker 1 fills 40 USDC at $0.45
  → taker 2 fills 30 USDC at $0.45
  → 30 USDC remains on the book
  → Status: PARTIAL
```

You have two options for the remainder:

| Option     | When                                               |
| ---------- | -------------------------------------------------- |
| **Wait**   | Let the rest fill at the same price                |
| **Cancel** | Take what's filled, get the remaining 30 USDC back |

Filled portions are **immediately yours** — you don't need to wait for the order to be 100% filled before holding the shares.

### Automatic Cancellation

Orders are automatically cancelled in two scenarios — you don't need to do anything:

| Scenario                      | What happens                                                |
| ----------------------------- | ----------------------------------------------------------- |
| **Market reaches `endTime`**  | All open orders for that market cancel; locked funds return |
| **Market enters refund mode** | All open orders cancel; locked funds return                 |

You'll receive a notification when this happens. Check Portfolio → History to see the `OrderCancelled` event with reason `MARKET_ENDED` or `REFUND_MODE`.

{% hint style="info" %}
**You'll be warned before `endTime`.** The app sends a notification 24h, 1h, and 5 minutes before market endTime if you still have open orders. Decide whether to cancel manually or let them auto-cancel.
{% endhint %}

### Order Status

| Status        | Meaning                                                |
| ------------- | ------------------------------------------------------ |
| **OPEN**      | Resting on the orderbook, no fills yet                 |
| **PARTIAL**   | Filled in part, remainder still resting                |
| **PENDING**   | Match in progress (block not yet confirmed)            |
| **FILLED**    | Fully matched — appears in History, not in Open Orders |
| **CANCELLED** | Cancelled by you or auto-cancel — funds returned       |

### Order Limits

| Parameter                    | Value                                     |
| ---------------------------- | ----------------------------------------- |
| **Max open orders per user** | `100` per market                          |
| **Max total open orders**    | `500` across all markets                  |
| **Min order size**           | `1 USDC` (BUY) or `1 share` (SELL)        |
| **Tick size**                | `$0.01` (prices like `0.45`, not `0.455`) |

If you hit these limits, you'll need to cancel existing orders before placing new ones.

### Notifications

The app notifies you when:

* An order **fills** (full or partial)
* An order is **cancelled** (manual, auto, or refund mode)
* An order is **nearing endTime** (24h / 1h / 5min warnings)
* An order is **stale** (more than 7 days old with no fills)

Toggle notification settings in **Settings → Notifications**.
