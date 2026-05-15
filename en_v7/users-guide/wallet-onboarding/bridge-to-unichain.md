---
description: >-
  Easily bridge USDC to Unichain on PrediX via our in-app widget, direct CEX
  withdrawals, and gas-free Paymaster solutions.
icon: bridge-circle-check
---

# Bridge to Unichain

PrediX uses USDC on Unichain as collateral. If your USDC is on another chain, you need to bridge it over.

## Supported Source Chains

| Chain                               | Default bridge in app                                                                                 | Time         | Estimated fee         |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------- | ------------ | --------------------- |
| **Ethereum**                        | Across, Stargate                                                                                      | 5-15 min     | $2-8 (depends on gas) |
| **Base**                            | Across, Superbridge                                                                                   | 2-5 min      | $0.5-2                |
| **Arbitrum**                        | Across, Stargate                                                                                      | 2-5 min      | $0.5-2                |
| **Optimism**                        | Across, Superbridge                                                                                   | 2-5 min      | $0.5-2                |
| **Polygon**                         | LayerZero, Across                                                                                     | 5-10 min     | $1-3                  |
| **CEX** (Binance, Coinbase, OKX...) | Withdraw directly to Unichain (if supported by your CEX) or withdraw to Ethereum/Arbitrum then bridge | See CEX docs | CEX withdrawal fee    |

{% hint style="info" %}
**Tip**: Coinbase and Binance have added or are adding Unichain network support.&#x20;

Direct withdrawal saves you a bridge step.
{% endhint %}

## Bridge Widget (in-app)

PrediX UI has an integrated **Bridge widget** — no need to open Across/Stargate in a separate tab.

![Bridge flow: select source chain → connect wallet → enter amount → approve → deposit → wait for relay → USDC arrives on Unichain](../../.gitbook/assets/12-bridge-flow.svg)

## How to Bridge USDC to Unichain

{% stepper %}
{% step %}
### <mark style="color:orange;">Step 1: Open the Deposit Menu</mark>

* Navigate to the app header and click on Deposit.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 2: Choose the Bridge Option</mark>

* From the available methods, select Bridge from another chain.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 3: Configure Your Transfer</mark>

* Select your Source Chain (e.g., Ethereum, Arbitrum, Base).
* Enter the USDC amount you wish to transfer.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 4: Review the Quote</mark>

Check the generated quote for:

* Fees: The cost of the bridge transaction.
* Time Estimate: How long the transfer will take.
* Final Amount: The exact USDC you will receive on Unichain.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 5: Approve the Token</mark>

* Approve USDC on the source chain to allow the bridge to move your funds.

{% hint style="info" %}
Note: This is usually a one-time action per token. The system uses Permit2 if supported to streamline the process.
{% endhint %}
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 6: Execute the Deposit</mark>

* Click Deposit and sign the transaction in your wallet on the source chain.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 7: Monitor Status</mark>

* The app will automatically switch your network to Unichain and poll for the transaction status.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 8: Completion</mark>

Once the process is complete, the USDC will arrive in your Unichain wallet. You are now ready to trade.
{% endstep %}
{% endstepper %}

## How to Withdraw Directly from a CEX to Unichain

Many major exchanges (such as Coinbase) now support native withdrawals to Unichain, allowing you to skip the manual bridging process and save on fees.

{% stepper %}
{% step %}
### <mark style="color:orange;">Step 1: Start the Withdrawal</mark>

* On your CEX account, navigate to your wallet/assets and select USDC.
* Click on Withdraw.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 2: Select the Correct Network</mark>

* When prompted to choose a network, select Unichain.

{% hint style="info" %}
Note: If Unichain is not yet listed on your specific CEX, you will need to withdraw to Ethereum or Arbitrum first, and then use the Bridge process to move it to Unichain.
{% endhint %}
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 3: Enter Your Destination Address</mark>

* Copy your Unichain wallet address from the PrediX UI.
* Paste it into the recipient address field on the CEX.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 4: Verify Security</mark>

* Enter the amount you wish to withdraw.
* Confirm the transaction using your 2FA (Authenticator app) or Email verification.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 5: Wait for Delivery</mark>

* Your funds will be processed by the CEX.
* This typically takes 5–30 minutes depending on the exchange's internal review and network congestion. Once confirmed, your USDC will appear in your PrediX wallet.
{% endstep %}
{% endstepper %}

{% hint style="danger" icon="circle-star" %}
**Important**: Test with a small amount ($10) the first time. Wrong network = lost funds with no recovery.
{% endhint %}

## Gas Fees (native ETH)

To pay transaction fees on Unichain you need a small amount of **ETH on Unichain**:

| Wallet type                 | Gas mechanism                                                                        |
| --------------------------- | ------------------------------------------------------------------------------------ |
| **Passkey + Smart Account** | Paymaster auto-pays in USDC. Sponsorship covers gas if eligible — no ETH needed.     |
| **Crypto Wallet (EOA)**     | You pay ETH directly. Bridge ETH using the same widget — select ETH instead of USDC. |

{% hint style="success" %}
**Unichain gas is extremely cheap** — typical trade tx costs **\~$0.001–0.01**. A one-time bridge of $5 worth of ETH lasts thousands of transactions for EOA users.
{% endhint %}

## Reverse Bridge (Unichain → another chain)

Same UI, opposite direction:

{% stepper %}
{% step %}
### <mark style="color:orange;">Step 1: Open the Withdrawal Menu</mark>

* Navigate to the app header and click on Withdraw.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 2: Select the Bridge Option</mark>

* From the available methods, select Bridge to another chain.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 3: Configure Your Transfer</mark>

* Choose your Destination Chain (e.g., Ethereum, Base, or Arbitrum).
* Enter the Amount of USDC you wish to bridge.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 4: Authorize the Transaction</mark>

* Review the estimated fees and arrival time.
* Sign the transaction in your wallet on the Unichain network.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 5: Finalize and Wait</mark>

* Monitor the progress bar within the app.
* Wait for completion: The transfer typically takes 2–15 minutes before the funds appear on your destination chain.
{% endstep %}
{% endstepper %}

***

## Slippage & Quote

Bridges involve **slippage** (USDC prices across chains have a small spread).

* **Across, Stargate**: typically 0.05-0.2% slippage.
* The app quote shows: amount out **after deducting fees + slippage**.
* If prices move significantly during the bridge, you may receive a refund to the source chain (Across/LayerZero handle this case).

## If the Bridge Gets Stuck

* Across, Stargate: usually auto-complete within 1-30 minutes. If nothing after 1 hour: check the source chain explorer (tx confirmed?), check the destination chain (UserOp/relay arrived?).
* Contact bridge support directly — PrediX does not operate bridges, only integrates the UI.
* Need help: [Discord](../../resources/links.md) #bridge-support.

## Safety Questions

<details>

<summary><mark style="color:orange;"><strong>Is bridging safe?</strong></mark> </summary>

PrediX integrates bridges with billions in TVL that have undergone multiple audit rounds (Across, Stargate, LayerZero). However, cross-chain bridges are **the largest attack vector in DeFi history** ($2B+ exploited 2022-2024). Only bridge the amount you need — do not hold funds long-term on bridge contracts.

</details>

<details>

<summary><mark style="color:orange;"><strong>Does PrediX hold my funds during bridging?</strong></mark></summary>

No. The bridge contracts belong to third parties (Across, Stargate). PrediX only provides the UI for convenience.

</details>

<details>

<summary><mark style="color:orange;"><strong>Where do bridge fees go?</strong></mark></summary>

&#x20;To the relayers / LPs of the respective bridge protocol. PrediX does not charge bridge fees.

</details>
