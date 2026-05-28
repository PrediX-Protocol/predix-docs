---
description: View, cancel, and track your open limit orders.
---

# Managing Orders

Take full control of your PrediX Limit Orders. Easily view statuses, execute batch cancellations, track partial fills, and manage your portfolio with automated notifications.

After placing a limit order, you can monitor it, cancel it, or wait for it to fill. PrediX gives you full control over your resting orders - single cancel, batch cancel, and automatic cleanup when markets resolve.

### Viewing Open Orders

Navigate to **Portfolio → Orders** to view all your active orders across every market. Each row shows:

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="image">Cover image</th></tr></thead><tbody><tr><td><mark style="color:orange;"><strong>Market</strong></mark></td><td>The market the order is placed on</td><td><a href="../.gitbook/assets/25.png">25.png</a></td></tr><tr><td><mark style="color:orange;"><strong>Side</strong></mark></td><td>YES or NO</td><td><a href="../.gitbook/assets/27.png">27.png</a></td></tr><tr><td><mark style="color:orange;"><strong>Direction</strong></mark></td><td>Buy or Sell</td><td><a href="../.gitbook/assets/26 (1).png">26 (1).png</a></td></tr><tr><td><mark style="color:orange;"><strong>Price</strong></mark></td><td>Your limit price (e.g., <code>$0.45</code>)</td><td><a href="../.gitbook/assets/28.png">28.png</a></td></tr><tr><td><mark style="color:orange;"><strong>Filled</strong></mark></td><td>Amount already matched (for partial fills)</td><td><a href="../.gitbook/assets/29.png">29.png</a></td></tr><tr><td><mark style="color:orange;"><strong>Remaining</strong></mark></td><td>Size still resting on the book</td><td><a href="../.gitbook/assets/34.jpg">34.jpg</a></td></tr><tr><td><mark style="color:orange;"><strong>Status</strong></mark></td><td><code>OPEN</code>, <code>PARTIAL</code>, or <code>PENDING</code></td><td><a href="../.gitbook/assets/30.png">30.png</a></td></tr><tr><td><mark style="color:orange;"><strong>Action</strong></mark></td><td>Cancel button</td><td><a href="../.gitbook/assets/31.png">31.png</a></td></tr></tbody></table>

You can also view orders for a specific market on the market detail page, under the **Orderbook** tab → **My Orders**.

***

### Cancelling Orders

#### How to Cancel a Single Order

{% stepper %}
{% step %}
<mark style="color:orange;">**Step 1: Locate Your Order**</mark>

* Navigate to your Portfolio and select the Orders tab.
* Alternatively, go to the specific market's detail page and find the My Orders tab.
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 2: Initiate Cancellation**</mark>

* Find the specific order you wish to close.
* Click the Cancel button located next to the order details.
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 3: Confirm in Wallet**</mark>

* A prompt will appear in your wallet (e.g., Biometric scan for Passkeys or a pop-up for MetaMask).
* Confirm the transaction to authorize the cancellation.
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 4: Funds Released**</mark>

* Once the transaction is processed, your locked USDC or tokens are immediately returned to your wallet balance within the same transaction.
{% endstep %}
{% endstepper %}

{% hint style="info" %}
**No protocol fee** on cancel. Gas is paid by the user; paymaster sponsorship applies if you qualify (both Passkey and EOA wallets eligible).
{% endhint %}

#### Cancel Multiple Orders

Select multiple orders with checkboxes, then click **Cancel Selected**:

| Wallet Type                                                                               | Behavior                                                                                             |
| ----------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| [Passkey + Smart Account](wallet-setup/connect-wallet.md#method-1-passkey--smart-account) | <ul><li>All cancels bundled into 1 UserOp - single confirmation, single gas (or sponsored)</li></ul> |
| [Web3 Wallet (EOA)](wallet-setup/connect-wallet.md#method-2-crypto-wallet-eoa)            | <ul><li>One transaction per order - N confirmations</li></ul>                                        |

***

#### Cancel All Orders

Click **Cancel All** at the top of the Orders tab to cancel **every open order across every market** in one click.

| Wallet Type                                                                               | Behavior                                                  |
| ----------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| [Passkey + Smart Account](wallet-setup/connect-wallet.md#method-1-passkey--smart-account) | <ul><li>1 UserOp bundles all cancels atomically</li></ul> |
| [Web3 Wallet (EOA)](wallet-setup/connect-wallet.md#method-2-crypto-wallet-eoa)            | <ul><li>Sequential transactions (one per order)</li></ul> |

{% hint style="warning" %}
**Cancel All is irreversible.** Once confirmed, all your resting limit orders are gone. If you change your mind, you'll need to re-place each order manually.
{% endhint %}

#### Programmatic Cancel (API)

For bots and automated strategies, the same cancel functions are available via API:

* Cancel a single order
* Cancel orders in batch
* Cancel all orders in a market

***

### Editing Orders

PrediX **does not support direct order editing**. To change an order's price or size, you must:

1. **Cancel** the existing order
2. **Place a new order** with the updated parameters

For bots that frequently update quotes, the API supports `cancelAndReplace` - a single atomic operation that cancels and re-places in one transaction. See API → Replace Order.

***

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

Filled portions are **immediately yours** - you don't need to wait for the order to be 100% filled before holding the shares.

***

### Automatic Cancellation

Orders are automatically cancelled in two scenarios - you don't need to do anything:

| Scenario                      | What happens                                                                  |
| ----------------------------- | ----------------------------------------------------------------------------- |
| **Market reaches `endTime`**  | <ul><li>All open orders for that market cancel; locked funds return</li></ul> |
| **Market enters refund mode** | <ul><li>All open orders cancel; locked funds return</li></ul>                 |

You'll receive a notification when this happens. Check Portfolio → History to see the `OrderCancelled` event with reason `MARKET_ENDED` or `REFUND_MODE`.

{% hint style="info" %}
**You'll be warned before `endTime`.** The app sends a notification 24h, 1h, and 5 minutes before market endTime if you still have open orders. Decide whether to cancel manually or let them auto-cancel.
{% endhint %}

***

### Order Status

| Status        | Meaning                                                                  |
| ------------- | ------------------------------------------------------------------------ |
| **OPEN**      | <ul><li>Resting on the orderbook, no fills yet</li></ul>                 |
| **PARTIAL**   | <ul><li>Filled in part, remainder still resting</li></ul>                |
| **PENDING**   | <ul><li>Match in progress (block not yet confirmed)</li></ul>            |
| **FILLED**    | <ul><li>Fully matched - appears in History, not in Open Orders</li></ul> |
| **CANCELLED** | <ul><li>Cancelled by you or auto-cancel - funds returned</li></ul>       |

***

### Order Limits

| Parameter                    | Value                                                                                        |
| ---------------------------- | -------------------------------------------------------------------------------------------- |
| **Max open orders per user** | <ul><li><code>50</code> per market</li></ul>                                                 |
| **Max total open orders**    | <ul><li><code>500</code> across all markets</li></ul>                                        |
| **Min order size**           | <ul><li><code>1 USDC</code> (BUY) or <code>1 share</code> (SELL)</li></ul>                   |
| **Tick size**                | <ul><li><code>$0.01</code> (prices like <code>0.45</code>, not <code>0.455</code>)</li></ul> |

If you hit these limits, you'll need to cancel existing orders before placing new ones.

***

### Notifications

The app notifies you when:

* An order **fills** (full or partial)
* An order is **cancelled** (manual, auto, or refund mode)
* An order is **nearing endTime** (24h / 1h / 5min warnings)
* An order is **stale** (more than 7 days old with no fills)

Toggle notification settings in [Notifications & Alerts](features/notifications-and-alerts.md).
