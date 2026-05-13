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
