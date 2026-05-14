---
description: >-
  Take full control of your PrediX Limit Orders. Easily view statuses, execute
  batch cancellations, track partial fills, and manage your portfolio with
  automated notifications.
---

# Managing Orders

View, cancel, and track your open limit orders.

### Overview

After placing a limit order, you can monitor it, cancel it, or wait for it to fill. PrediX gives you full control over your resting orders — single cancel, batch cancel, and automatic cleanup when markets resolve.

### Viewing Open Orders

Navigate to **Portfolio → Orders** to view all your active orders across every market. Each row shows:

<table data-view="cards"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><mark style="color:orange;"><strong>Market</strong></mark></td><td>The market the order is placed on</td></tr><tr><td><mark style="color:orange;"><strong>Side</strong></mark></td><td>YES or NO</td></tr><tr><td><mark style="color:orange;"><strong>Direction</strong></mark></td><td>Buy or Sell</td></tr><tr><td><mark style="color:orange;"><strong>Price</strong></mark></td><td>Your limit price (e.g., <code>$0.45</code>)</td></tr><tr><td><mark style="color:orange;"><strong>Size</strong></mark></td><td>Original order size</td></tr><tr><td><mark style="color:orange;"><strong>Filled</strong></mark></td><td>Amount already matched (for partial fills)</td></tr><tr><td><mark style="color:orange;"><strong>Remaining</strong></mark></td><td>Size still resting on the book</td></tr><tr><td><mark style="color:orange;"><strong>Status</strong></mark></td><td><code>OPEN</code>, <code>PARTIAL</code>, or <code>PENDING</code></td></tr><tr><td><mark style="color:orange;"><strong>Placed at</strong></mark></td><td>Timestamp</td></tr><tr><td><mark style="color:orange;"><strong>Action</strong></mark></td><td>Cancel button</td></tr></tbody></table>

You can also view orders for a specific market on the market detail page, under the **Orderbook** tab → **My Orders**.

### Cancelling Orders

#### <mark style="color:orange;">How to Cancel a Single Order</mark>

{% stepper %}
{% step %}
### <mark style="color:orange;">Step 1: Locate Your Order</mark>

* Navigate to your Portfolio and select the Orders tab.
* Alternatively, go to the specific market's detail page and find the My Orders tab.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 2: Initiate Cancellation</mark>

* Find the specific order you wish to close.
* Click the Cancel button located next to the order details.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 3: Confirm in Wallet</mark>

* A prompt will appear in your wallet (e.g., Biometric scan for Passkeys or a pop-up for MetaMask).
* Confirm the transaction to authorize the cancellation.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 4: Funds Released</mark>

* Once the transaction is processed, your locked USDC or tokens are immediately returned to your wallet balance within the same transaction.
{% endstep %}
{% endstepper %}

{% hint style="info" %}
**No protocol fee** on cancel. Gas is paid by the user; paymaster sponsorship applies if you qualify (both Passkey and EOA wallets eligible).
{% endhint %}

#### <mark style="color:orange;">Cancel Multiple Orders</mark>

Select multiple orders with checkboxes, then click **Cancel Selected**:

| Wallet                      | Behavior                                                                           |
| --------------------------- | ---------------------------------------------------------------------------------- |
| **Passkey + Smart Account** | All cancels bundled into 1 UserOp — single confirmation, single gas (or sponsored) |
| **Crypto Wallet (EOA)**     | One transaction per order — N confirmations                                        |

#### <mark style="color:orange;">Cancel All Orders</mark>

Click **Cancel All** at the top of the Orders tab to cancel **every open order across every market** in one click.

| Wallet      | Behavior                                |
| ----------- | --------------------------------------- |
| **Passkey** | 1 UserOp bundles all cancels atomically |
| **EOA**     | Sequential transactions (one per order) |

{% hint style="warning" %}
**Cancel All is irreversible.** Once confirmed, all your resting limit orders are gone. If you change your mind, you'll need to re-place each order manually.
{% endhint %}

#### <mark style="color:orange;">Programmatic Cancel (API)</mark>

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
