---
description: >-
  Connect your wallet to access the PrediX Orderbook and AMM. You maintain full
  control of your assets while trading at the best market prices.
icon: wallet
---

# Connect wallet

PrediX offers 2 sign-in methods. Both are non-custodial — nobody (including PrediX) holds your private key.

## Decision Tree

![Wallet selection flow: Passkey (smart account, biometric) or Crypto wallet (EOA, MetaMask/Ledger) — both non-custodial](../../.gitbook/assets/13-wallet-connect.svg)

## Comparison

|                        | Passkey + Smart Account                          | Crypto Wallet (EOA)                                   |
| ---------------------- | ------------------------------------------------ | ----------------------------------------------------- |
| **Experience**         | Web2-like, biometric                             | Traditional web3, sign each tx                        |
| **Browser extension**  | Not required                                     | Required (MetaMask, Rainbow...)                       |
| **Backup & recovery**  | Cloud sync (iCloud / Google), or a second device | BIP-39 seed phrase (12-24 words)                      |
| **Hardware wallet**    | No (private key in Secure Enclave)               | Yes (Ledger, Trezor)                                  |
| **Gas fees**           | Pay via paymaster (USDC)                         | Pay ETH directly (Unichain is cheap \~$0.001-0.01/tx) |
| **Batch transactions** | Yes (1-click `[approve, swap]` atomic)           | No (2 separate txs)                                   |
| **First-time sign-up** | \~5 seconds biometric                            | Already have a wallet → instant                       |
| **Best for**           | New users · fast onboarding                      | DeFi users · large custody · hardware wallets         |

By default, both methods require users to pay their own gas. PrediX has a sponsorship program for eligible users (new user onboarding, stakers above threshold, campaign-eligible events) — **applies to both account types**:

<table data-view="cards"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><mark style="color:orange;"><strong>Smart account</strong></mark> </td><td>→ paymaster covers gas directly</td></tr><tr><td><mark style="color:orange;"><strong>EOA</strong></mark> </td><td>→ off-chain rebate/refund (mechanism announced pre-launch)</td></tr></tbody></table>

Eligibility criteria and sponsorship duration may change via governance vote.

## A — Passkey + Smart Account

Passkeys use the **WebAuthn** standard — biometric (Touch ID, Face ID, Windows Hello) or your device PIN for authentication. The private key is generated and stored in the **Secure Enclave / TPM** and cannot be exported.

On sign-up, the app deploys a **Kernel smart account (ERC-4337)** — an on-chain wallet contract that validates via passkey signature. All actions go through UserOps.

### <mark style="color:orange;">Create New</mark>

{% stepper %}
{% step %}
### <mark style="color:orange;">Step 1: Access the Portal</mark>

* Open your browser and navigate to [_app.predix.app_](https://app.predix.app).
* Click the **Sign up** button to begin.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 2: Choose Authentication Method</mark>

* From the available options, select Continue with passkey.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 3: Biometric Verification</mark>

* Your browser will trigger a system prompt for biometric authentication (such as FaceID, TouchID, or Windows Hello).
* Confirm the prompt to verify your identity securely.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 4: Smart Account Generation</mark>

* Your Smart account counterfactual address will appear immediately.

{% hint style="info" %}
Note: Your account is officially deployed on-chain the moment you perform your first action.
{% endhint %}
{% endstep %}
{% endstepper %}

#### <mark style="color:orange;">**Backup**</mark>

Choose at least one backup method on sign-up:

<table data-view="cards"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><mark style="color:orange;"><strong>iCloud Keychain</strong> (iPhone, Mac)</mark></td><td>passkey syncs across Apple devices with the same Apple ID</td></tr><tr><td><mark style="color:orange;"><strong>Google Password Manager</strong> (Android, Chrome)</mark></td><td>syncs across devices</td></tr><tr><td><mark style="color:orange;"><strong>Hardware key</strong> (YubiKey, Titan</mark></td><td>passkey stored on hardware key, plug-in to authenticate</td></tr></tbody></table>

{% hint style="warning" %}
**Single point of failure.** If you only have 1 device + no cloud sync + no hardware key backup → **losing the device = losing the wallet**. Enable cloud sync or add a second device immediately after sign-up.
{% endhint %}

#### <mark style="color:orange;">**Recovery (lost device)**</mark>

| Scenario             | What to do                                                                                       |
| -------------------- | ------------------------------------------------------------------------------------------------ |
| Cloud sync enabled   | Sign in on a new device with the same Apple ID / Google account — passkey restores automatically |
| Second device paired | Use the second device to authenticate, then re-pair a new device                                 |
| Hardware key backup  | Plug in the hardware key on any device to restore access                                         |
| None of the above    | ❌ Wallet is unrecoverable                                                                        |

{% hint style="success" %}
**Roadmap — Social recovery.** Designate N trusted guardians; any M-of-N can co-sign to restore access. TBA post-mainnet.
{% endhint %}

## B — Crypto Wallet (EOA)

Use a traditional wallet you already own — MetaMask, Rainbow, Coinbase Wallet, or any wallet that supports WalletConnect / hardware wallet.

#### <mark style="color:orange;">**How to Connect and Trade via Web3 Wallet**</mark>

{% stepper %}
{% step %}
### <mark style="color:orange;">Step 1: Initiate Connection</mark>

* Navigate to the app header and click the Connect wallet button.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 2: Select Your Provider</mark>

* Choose your preferred wallet from the list (e.g., MetaMask, Rainbow, WalletConnect, or Ledger).
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 3: Approve the Request</mark>

* Your wallet extension or app will trigger a pop-up.
* Approve the connection request to link your wallet to the platform.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 4: Secure Sign-In (SIWE)</mark>

* Sign the SIWE (Sign-In With Ethereum) message to verify ownership of your address.

{% hint style="info" %}
Note: This is an off-chain signature and incurs no gas cost.
{% endhint %}
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 5: Start Trading</mark>

* You are now ready to trade directly.
* Please ensure you have sufficient funds, as each transaction requires gas fees paid in ETH on Unichain.
{% endstep %}
{% endstepper %}

#### <mark style="color:orange;">When to use EOA</mark>

* You already have a DeFi workflow with MetaMask + hardware wallet
* Large custody balance — want standard BIP-39 seed phrase backup
* Integration with other tooling (Frame, Rabby, Safe multisig)
* Prefer full control with no paymaster dependency

***

### SIWE Session

Both methods use **SIWE (EIP-4361)** to create a session with the backend:

![SIWE auth flow: FE requests challenge → user signs message → BE verifies ECDSA → sets HTTPOnly cookie session for 7 days](../../.gitbook/assets/14-siwe-auth.svg)

### Troubleshooting

<details>

<summary><mark style="color:orange;">Passkey prompt does not appear</mark></summary>

Your browser may not support WebAuthn. Use Chrome, Safari, Brave, or Edge (latest version). On iOS/Android, ensure device biometrics (Face ID / Touch ID / fingerprint) are enabled in system settings.

</details>

<details>

<summary><mark style="color:orange;">"Passkey not found" on a new device</mark></summary>

Sign in with the same Apple ID (iCloud Keychain) or Google account (Password Manager) used at sign-up. If you didn't enable cloud sync, use a second paired device or your hardware security key.

</details>

<details>

<summary><mark style="color:orange;">MetaMask not detected</mark></summary>

Make sure the MetaMask extension is installed and unlocked. Refresh the page after unlocking. On mobile, use MetaMask's in-app browser instead of Safari/Chrome.

</details>

<details>

<summary><mark style="color:orange;">Wrong network — "Switch to Unichain" prompt</mark></summary>

Click the prompt to switch automatically, or manually add Unichain to your wallet:

* **Network:** Unichain (or Unichain Sepolia for testnet)
* **Chain ID:** `130` (mainnet) / `1301` (testnet)
* **RPC:** `https://mainnet.unichain.org` / `https://sepolia.unichain.org`

</details>

<details>

<summary><mark style="color:orange;">WalletConnect QR code not scanning</mark></summary>

Make sure your wallet app is updated to the latest version. If the QR doesn't load, click "Copy to clipboard" and paste into your wallet's WalletConnect input.

</details>

<details>

<summary><mark style="color:orange;">SIWE signature rejected by wallet</mark></summary>

Some wallets block SIWE messages by default. In MetaMask, ensure "Sign typed data" warnings are not blocking — accept the message. The SIWE message is safe and contains no transaction data.

</details>
