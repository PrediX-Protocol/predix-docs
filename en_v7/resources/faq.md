# FAQ

## Product

### What is a prediction market?

An exchange for trading tokens that reflect the probability of a real-world event occurring. Each event has 2 tokens: YES and NO. When the market resolves, the winning token = $1 USDC, the losing token = $0.

If YES = $0.68 → the market is pricing a 68% probability that the event will happen.

### Why does YES + NO always equal \~$1? <a href="#yes-no-1-dollar" id="yes-no-1-dollar"></a>

Because you can **split** 1 USDC → mint 1 YES + 1 NO at any time (atomic, on-chain).

* YES + NO > $1 → arbitrageurs split USDC, sell both → capture spread → push price down.
* YES + NO < $1 → arbitrageurs buy both, merge → capture spread → push price up.

Auto-balances at \~$1 (small deviation due to AMM curve + spread).

### How is PrediX different from Polymarket?

|                 | PrediX                                     | Polymarket                     |
| --------------- | ------------------------------------------ | ------------------------------ |
| Outcome token   | ERC-20 (composable)                        | ERC-1155                       |
| Liquidity       | CLOB + AMM hybrid                          | CLOB only (off-chain matching) |
| Chain           | Unichain (L2)                              | Polygon                        |
| Account         | Passkey + smart account (AA) or EOA wallet | EOA + Magic Link               |
| Geo restriction | Per regulation by region, no default block | Banned in 33+ countries        |
| Token economy   | PRX with real yield + buyback-burn         | UMA staking, no native token   |

### Can I trade from anywhere?

PrediX is a decentralized on-chain protocol. Access it from anywhere with an internet connection. Depending on your region, **local regulations** may apply — check your jurisdiction. The PrediX UI applies geo-restrictions per compliance requirements.

## Wallet & Account

### Passkey vs crypto wallet — which one? <a href="#passkey-vs-metamask" id="passkey-vs-metamask"></a>

PrediX offers 2 methods:

* **Passkey + Smart Account** (ERC-4337): Most convenient, web2-like UX, no extension needed. Biometric (Touch ID / Face ID) to unlock. Backup via cloud sync or second device. Supports gas sponsorship via paymaster (for eligible users). Best for new users, small-to-medium balances.
* **Crypto wallet (EOA)** — MetaMask, Rainbow, Coinbase Wallet, WalletConnect, Ledger: Recovery via BIP-39 seed phrase, compatible with hardware wallets. Pay gas in ETH. Best for experienced DeFi users, large custody, or integration with other tooling.

Both are **non-custodial** — PrediX never holds your private key.

### What if I lose the device with my passkey? <a href="#lost-passkey" id="lost-passkey"></a>

* Have cloud sync (iCloud / Google Password) → reinstall the app on a new device, sign into your cloud account, passkey syncs automatically.
* Have a second device with synced passkey → open the app on that device.
* Have nothing → wallet is lost. Phase 2 will introduce **social recovery** (guardian-based).

**Tip**: Set up 2 devices with passkey from the start, or enable cloud sync.

### Can I export the seed phrase from a passkey wallet?

No. The passkey private key resides in the Secure Enclave / TPM and is non-exportable by design. If you need standard BIP-39 seed phrase backup, use a **crypto wallet (EOA)** instead of passkey.

### What is my smart account address?

After signing in for the first time, the app displays your address. This is a **counterfactual address** — it exists even before any on-chain tx is deployed. Any deposits sent to this address are safe.

## Gas & Fees

### Do I need to pay gas? <a href="#gas-fees" id="gas-fees"></a>

By default, **both methods require users to pay their own gas**. PrediX has a **gas sponsorship program** for eligible users (e.g., new user onboarding, stakers above a certain threshold, campaign events) — **applies to both account types**, regardless of wallet type:

* **Passkey + Smart Account**: Gas paid via paymaster. If eligible for sponsorship → paymaster pays on behalf of the user — fully gasless UX for core actions (swap, split, merge, redeem, place/cancel order).
* **Crypto wallet (EOA)**: Gas paid in ETH on Unichain. If eligible for sponsorship → rebate/refund mechanism (details announced pre-launch). Unichain gas is very cheap — typically $0.001-0.01 per tx even when paying yourself.

Eligibility criteria and sponsorship duration will be announced pre-launch and may change via governance vote.

## Funding

### Can I deposit USDC from Binance / Coinbase? <a href="#deposit-from-cex" id="deposit-from-cex"></a>

Yes. Many CEXs have added Unichain network — withdraw directly.

If your CEX doesn't support it yet: withdraw to Ethereum or Arbitrum, then use the [Bridge widget](/broken/pages/N6PP2E6mV0vPeJ9TkAjs) in the app. Coinbase, Binance, OKX, and Bybit mostly support Unichain or chains with easy bridging.

### Is bridging safe?

PrediX integrates bridges with billions in TVL: Across, Stargate, LayerZero. All have been audited multiple times.

**Note**: Cross-chain bridges are the largest attack vector in DeFi history. Only bridge the amount you need — do not hold funds long-term on a bridge. Test with a small amount before bridging larger sums.

### What is the minimum deposit?

* The app does not enforce a minimum.
* However: below $5, gas + bridge fees may represent a large percentage of the amount.
* Recommended: $20+ for cost efficiency.

## Trading

### What is slippage?

The difference between the preview price and the actual execution price when the tx is confirmed. Default tolerance is 0.5%.

If slippage is exceeded → tx **reverts**, no funds lost (gas is still consumed — covered by sponsorship if the user is eligible, regardless of account type).

### What is the minimum trade size?

* Market order: $1 USDC minimum.
* Limit order: $1 USDC to avoid dust orders.
* No maximum — the only limit is pool/CLOB liquidity.

### Is stop-loss / OCO supported?

Phase 2. Currently only standard market + limit orders are available.

### What does high slippage mean?

The market lacks liquidity. You have 3 options:

1. Reduce your order size.
2. Place a limit order and wait for a fill.
3. Become an LP to earn fees from the very trades you make.

## Resolution & Redemption

### How long does market resolution take?

* **Chainlink oracle**: Automatic after snapshotAt + 1 Chainlink round (\~30 seconds - 1 minute).
* **Manual oracle**: Multisig 2/3 signs, typically within 24 hours.
* **UMA oracle**: 48h dispute window + DVM if disputed.

### How long do I have to redeem?

No strict deadline, but there is a **365-day grace period**. After that, admin may call `sweepUnclaimed` to transfer unclaimed tokens to the treasury.

### What if a market can't be resolved?

Admin enables **refund mode** via a 48h timelock. Users burn their YES+NO pairs and receive USDC pro-rata.

## Security

### Have the contracts been audited?

Yes. Audit firm + public report available at [Security & timelock](../developers-guide/security.md). There is also a bug bounty ranging from $100 to $500k depending on severity.

### Can admin rug pull?

No. Diamond upgrades go through TimelockController with a **48h delay**. Hook upgrades go through a proxy with a 48h timelock, self-gated and monotonic. The community has 48 hours to verify and react (withdraw, fork) before any change executes.

`CUT_EXECUTOR_ROLE` belongs exclusively to the TimelockController contract — no EOA can bypass it.

### Is there an insurance fund?

Phase 2 (TBA). The protocol does not currently have an insurance pool.

### Does PrediX hold user funds?

No. The Router is stateless — `balanceOf(router) == 0` is enforced on-chain after every tx. User funds reside in:

* User wallet (USDC, YES, NO tokens)
* Diamond contract (collateral locked per market)
* LP position (if providing liquidity)
* Stake vault (if staking PRX)

## PRX Token

### When is TGE?

TBA. Follow [official Discord / Twitter](links.md) for announcements.

### What is the purpose of staking PRX?

Receive a share of protocol fees in **real USDC**, and vote on governance. Details: [Staking real yield](../economics/staking.md).

### What is the difference between vePRX and stkPRX?

* **stkPRX**: Stake without lock, receive USDC yield.
* **vePRX**: Stake + lock for 1-48 months, receive **boosted yield** + governance weight (gauge voting).

The longer you lock, the more yield + voting power you get. [vePRX & gauge](../economics/veprx-gauge.md).

### Airdrop?

There is a testnet points campaign that converts to PRX at TGE. Conversion ratio announced pre-TGE via official channels.

## Support

### Where do I report bugs?

* **Security bugs** (smart contract, exploits): [security@predix.app](mailto:security@predix.app), bug bounty paid by severity.
* **UI / app bugs**: GitHub issue or Discord #bug-report.

## Other Questions

* [Passkey vs MetaMask — which one?](faq.md#passkey-vs-metamask)
* [Why does YES + NO always equal \~$1?](faq.md#yes-no-1-dollar)
* [Are there gas fees?](faq.md#gas-fees)
* [Can I transfer USDC from Binance or Coinbase?](faq.md#deposit-from-cex)
* [What if I lose the device with my passkey?](faq.md#lost-passkey)

Discord, Twitter, Telegram — see [Links](links.md).

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

## Common First-Time Errors

* **"Insufficient USDC balance"** — Bridge USDC to Unichain first. See [Bridge](/broken/pages/N6PP2E6mV0vPeJ9TkAjs).
* **"Slippage exceeded"** — Price moved beyond tolerance while the tx was pending. Increase slippage (default 0.5% → 1%) or retry.
* **"Wallet not connected"** — Click Sign in / Connect wallet in the header.
* **"Market paused"** — Rare — admin paused the market for security reasons. Check the notice in the UI.

## Common errors

| Error                    | Reason                             | Fix                                                                              |
| ------------------------ | ---------------------------------- | -------------------------------------------------------------------------------- |
| "Slippage exceeded"      | Price moved beyond tolerance       | Increase slippage or retry                                                       |
| "Insufficient liquidity" | CLOB + AMM lack sufficient depth   | Reduce size or use a [limit order](../users-guide/yes-no-markets/limit-order.md) |
| "Market paused"          | Admin paused for security reasons  | Check the UI notice                                                              |
| "Past endTime"           | Trading has closed for this market | Wait for resolution to redeem or get a refund                                    |
| "Insufficient USDC"      | Wallet lacks USDC                  | [Bridge](/broken/pages/N6PP2E6mV0vPeJ9TkAjs) or top up                           |

### Troubleshooting

<details>

<summary><mark style="color:orange;"><strong>"Order rejected — invalid tick size"</strong></mark></summary>

**Reason:** Price has more than 2 decimals or is `0.00` / `1.00`.

**Fix:** Round to the nearest `$0.01` and resubmit. Valid prices are `0.01, 0.02, ..., 0.99`.

</details>

<details>

<summary><mark style="color:orange;"><strong>"Order rejected — insufficient balance"</strong></mark></summary>

**Reason:** Your wallet does not have enough USDC (for BUY) or YES/NO shares (for SELL) to cover the order, factoring in existing open orders.

**Fix:**

* For BUY: Bridge more USDC or cancel an existing open order to free locked funds
* For SELL: check Portfolio for share balance, or use a Market order to acquire shares first

</details>

<details>

<summary><mark style="color:orange;"><strong>"Order rejected — insufficient allowance"</strong></mark></summary>

**Reason:** The Exchange contract is not yet authorized to spend your tokens.

**Fix:** Approve once per token via the prompt that appears on first order. Uses Permit2 where supported (single-signature approval, no separate tx).

</details>

<details>

<summary><mark style="color:orange;"><strong>Order never fills</strong></mark></summary>

**Reason:** Your limit price is too far from current market.

**Fix:**

* Check the orderbook depth — is your price even close to the best bid/ask?
* Re-quote closer to mid (cancel and replace at a tighter price)
* If you need immediate execution, switch to Market order

</details>

<details>

<summary><mark style="color:orange;"><strong>Order disappeared without filling</strong></mark></summary>

**Reason:** Market reached `endTime` — all open orders auto-cancel on resolution start. Locked funds are returned automatically.

**Fix:** Check Portfolio → History for the cancellation event. The market is now in resolution phase — wait for outcome, then redeem if applicable.

</details>

<details>

<summary><mark style="color:orange;"><strong>Partial fill but remainder not matching</strong></mark></summary>

**Reason:** Only part of your order was at a price the taker accepted; rest is too far from mid.

**Fix:** Cancel the remainder and re-place at a better price, or let it sit until the market moves to your level.

</details>

