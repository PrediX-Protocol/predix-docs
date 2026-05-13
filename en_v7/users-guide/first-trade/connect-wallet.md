# Connect wallet

PrediX offers **2 sign-in methods**. Both are **non-custodial** — nobody (including PrediX) holds your private key.

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

> By default, both methods require users to pay their own gas. PrediX has a sponsorship program for eligible users (new user onboarding, stakers above threshold, campaign-eligible events) — **applies to both account types**:
>
> * **Smart account** → paymaster covers gas directly
> * **EOA** → off-chain rebate/refund (mechanism announced pre-launch)
>
> Eligibility criteria and sponsorship duration may change via governance vote.

## A — Passkey + Smart Account

Passkeys use the **WebAuthn** standard — biometric (Touch ID, Face ID, Windows Hello) or your device PIN for authentication. The private key is generated and stored in the **Secure Enclave / TPM** and cannot be exported.

On sign-up, the app deploys a **Kernel smart account (ERC-4337)** — an on-chain wallet contract that validates via passkey signature. All actions go through UserOps.

### Create New

1. Open [https://app.predixpro.io](https://app.predixpro.io) → click **Sign up**
2. Select **Continue with passkey**
3. Browser prompts biometric authentication → confirm
4. Smart account counterfactual address appears immediately (deployed on-chain on first action)

#### Backup

Choose at least one backup method on sign-up:

* **iCloud Keychain** (iPhone, Mac) — passkey syncs across Apple devices with the same Apple ID
* **Google Password Manager** (Android, Chrome) — syncs across devices
* **Hardware key** (YubiKey, Titan) — passkey stored on hardware key, plug-in to authenticate

{% hint style="warning" %}
**Single point of failure.** If you only have 1 device + no cloud sync + no hardware key backup → **losing the device = losing the wallet**. Enable cloud sync or add a second device immediately after sign-up.
{% endhint %}

#### Recovery (lost device)

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

### Steps

1. Click **Connect wallet** in the app header
2. Select your wallet (MetaMask / Rainbow / WalletConnect / Ledger…)
3. Approve the connection request in your wallet
4. Sign the **SIWE message** (Sign-In With Ethereum) — off-chain, no gas cost
5. Trade directly — each tx pays gas in ETH on Unichain

#### When to use EOA

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

<summary>Passkey prompt does not appear</summary>

Your browser may not support WebAuthn. Use Chrome, Safari, Brave, or Edge (latest version). On iOS/Android, ensure device biometrics (Face ID / Touch ID / fingerprint) are enabled in system settings.

</details>

<details>

<summary>"Passkey not found" on a new device</summary>

Sign in with the same Apple ID (iCloud Keychain) or Google account (Password Manager) used at sign-up. If you didn't enable cloud sync, use a second paired device or your hardware security key.

</details>

<details>

<summary>MetaMask not detected</summary>

Make sure the MetaMask extension is installed and unlocked. Refresh the page after unlocking. On mobile, use MetaMask's in-app browser instead of Safari/Chrome.

</details>

<details>

<summary>Wrong network — "Switch to Unichain" prompt</summary>

Click the prompt to switch automatically, or manually add Unichain to your wallet:

* **Network:** Unichain (or Unichain Sepolia for testnet)
* **Chain ID:** `130` (mainnet) / `1301` (testnet)
* **RPC:** `https://mainnet.unichain.org` / `https://sepolia.unichain.org`

</details>

<details>

<summary>WalletConnect QR code not scanning</summary>

Make sure your wallet app is updated to the latest version. If the QR doesn't load, click "Copy to clipboard" and paste into your wallet's WalletConnect input.

</details>

<details>

<summary>SIWE signature rejected by wallet</summary>

Some wallets block SIWE messages by default. In MetaMask, ensure "Sign typed data" warnings are not blocking — accept the message. The SIWE message is safe and contains no transaction data.

</details>
