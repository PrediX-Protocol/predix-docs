---
description: >-
  A reference guide to all key terms, concepts, and components used across the
  PrediX protocol. Terms are listed alphabetically.
---

# Glossary

***

### A

**AA (Account Abstraction)** The ERC-4337 standard for smart-contract wallets. PrediX uses Kernel v3 smart accounts with a passkey validator (biometric signing). Smart accounts pay gas through a paymaster. → See: _Wallet Setup_, _Paymaster_.

**AMM (Automated Market Maker)** A liquidity pool that prices swaps using a mathematical formula. PrediX uses Uniswap v4 for YES-USDC and NO-USDC pools. Fee is 0.30% per swap, paid to LPs. → See: _CLOB + AMM Hybrid_, _Provide Liquidity_.

**Arbitrage** A risk-free trade that exploits price discrepancies. Example: when YES + NO < $1 → buy both → merge into $1 USDC → profit the difference. Arbitrage bots run continuously, keeping YES + NO ≈ $1.

### B

**Badge** An NFT (ERC-1155) earned upon reaching a milestone (volume, accuracy, streak). Displayed on profile and leaderboard. Some badges are locked (non-transferable) to prevent wash trading. → See: _Rewards & Gamification_.

**Bond** Collateral staked to propose an oracle resolution (UMA) or to create a market (Phase 3). Valid → bond refunded; incorrect/spam → bond slashed. → See: _Oracle_, _Create Market_.

**Bridge** Transfers tokens between chains. PrediX integrates Across, Stargate, and LayerZero to move USDC to Unichain. → See: _Bridge to Unichain_.

**Bundler** An ERC-4337 service that collects UserOps and submits them on-chain. PrediX uses the Pimlico bundler.

### C

**CLOB (Central Limit Order Book)** An on-chain order book where makers post prices and takers match them. PrediXExchange has 99 ticks ($0.01–$0.99), stored in a compressed bitmap. → See: _Limit Order_, _PrediX Market_.

**Cliff** A period during which no tokens are released in a vesting schedule. The PRX team allocation has a 12-month cliff. → See: _Allocation & Vesting_.

**Concentrated liquidity** LP liquidity provided within a narrow price range (instead of the full $0.01–$0.99 range). Higher fee earnings, but higher IL risk. → See: _Provide Liquidity_.

### D

**Diamond (EIP-2535)** A proxy pattern that splits a single contract into multiple facets. PrediX uses it to exceed the 24KB bytecode limit and to upgrade individual modules independently. → See: _Architecture & Contracts_.

### E

**endTime** The timestamp at which a market closes for trading. After endTime, the oracle resolution window opens.

**EOA (Externally Owned Account)** A traditional wallet controlled by a private key (MetaMask, Ledger). The opposite of a smart account.

**Event (Multi-outcome)** A container holding N mutually-exclusive child markets — exactly one member resolves YES = true. Example: "FIFA WC 2026 Winner" with 48 teams. → See: _Multi-Outcome Markets_.

### F

**Faucet** A dispenser of free test tokens for testnet. The PrediX faucet provides 0.0005 ETH + 10,000 test-USDC per 24h per wallet. → See: _Testnet Information_.

### G

**Gauge voting** A mechanism where vePRX holders vote to allocate LP subsidies to specific markets. Pools with more votes earn higher fees for their LPs. → See: _vePRX & Gauge Voting_.

### H

**Hook (Uniswap v4)** A smart contract that plugs into a v4 pool via callbacks (beforeSwap, beforeAddLiquidity, etc.). The PrediX Hook applies anti-sandwich identity verification and dynamic fees. → See: _CLOB + AMM Hybrid_.

### I

**IL (Impermanent Loss)** The loss an LP incurs relative to simply holding, caused by AMM rebalancing as prices move. In prediction markets, outcome-token prices are bounded ($0.01–$0.99), so IL follows a distinct pattern. IL is offset by fees — if volume is high enough, fees > IL → net profit. → See: _Provide Liquidity_.

**Invariant** A rule that is hard-enforced on-chain. PrediX has 7 critical invariants (INV-1 through INV-7). For example, INV-1: `YES.totalSupply == NO.totalSupply == totalCollateral`. → See: _Security & Timelock_.

### L

**LP (Liquidity Provider)** A user who provides liquidity to an AMM pool, receives an LP NFT, and earns swap fees. LPs on PrediX supply USDC + YES/NO.

**LP NFT** A token representing an LP position on Uniswap v4 (PositionManager). Managed in Portfolio → Liquidity tab.

### M

**Maker** A user who places a limit order that rests on the CLOB awaiting a match. Enjoys price improvement when matched by a taker.

**Market** The fundamental trading unit: a YES/NO question + an outcome-token pair + an oracle + an endTime.

**Match type (CLOB)** Three matching types on the Exchange:

* _Complementary_ — BUY\_YES ↔ SELL\_YES (most common)
* _Mint_ (synthetic) — BUY\_YES + BUY\_NO ≥ $1, mints a new pair
* _Merge_ (synthetic) — SELL\_YES + SELL\_NO ≤ $1, burns a pair

**Merge** Burns 1 YES + 1 NO → returns 1 USDC. The reverse of Split. Zero-fee; used for arbitrage or exiting a two-sided position. → See: _Split & Merge_.

**Multisig** A wallet requiring N-of-M signatures to execute. PrediX uses 2/3 admin + 2/3 operator + 2/3 oracle reporter (Gnosis Safe on mainnet).

### O

**Oracle** A source that reports real-world event outcomes on-chain. PrediX supports four types: Manual, Chainlink, UMA, and Committee. If the oracle is wrong, the market resolves incorrectly and users lose funds. → See: _Resolution & Oracle_.

**Outcome token (YES/NO)** An ERC-20 token representing one side of a market (YES or NO), with 6 decimals (matching USDC). A correct YES = $1, an incorrect YES = $0. Composable with DeFi. → See: _Outcome Token_.

### P

**Passkey** The WebAuthn authentication standard using biometrics (Touch ID, Face ID, Windows Hello). The private key lives in the Secure Enclave / TPM and cannot be exported. → See: _Wallet Setup_.

**Paymaster (ERC-4337)** An intermediary contract that pays gas for UserOps on behalf of the user. Smart accounts can pay gas via paymaster (USDC, sponsorship, etc.). PrediX runs a gas-sponsorship program for eligible users.

**Permit2** A Uniswap contract enabling a one-time ERC-20 approve plus off-chain signing for subsequent transactions (gasless approval). The PrediX Router uses Permit2. Canonical address: `0x000000000022D473030F116dDEE9F6B43aC78BA3`.

**Points** Activity rewards tracked off-chain (volume, accuracy, LP, referral, etc.). Convert to PRX on a pro-rata basis. Earlier participation earns more. → See: _Points — Earn PRX_.

**Prediction market** An exchange for tokens that reflect the probability of an event. Example: a YES price of $0.68 means the market prices a 68% probability of the event occurring. → See: _Prediction Context_.

**PRX** The governance and revenue-sharing token of PrediX. Total supply is 1 billion (1B), hard-capped, with no minting after TGE. → See: _PRX Utility_.

### Q

**Quadratic voting** A voting mechanism where `voting_power = sqrt(vePRX_weight)` — reducing whale dominance. Planned for Phase 2, applied to proposals over $100k.

### R

**Refund mode** An emergency state triggered when an oracle fails (down, dispute hung). Users burn YES+NO pairs to receive USDC pro-rata. Admin enables it via a 48h timelock. → See: _Redeem & Refund_.

**Reorg (Reorganization)** A blockchain reorganization caused by a fork. The Ponder indexer handles it automatically — it reverts and replays, so clients need no custom logic.

**Router (PrediXRouter)** A stateless aggregator — the single entry point for all swaps. It automatically splits an order between the CLOB and the AMM. Invariant: `balanceOf(router) == 0` after every public call. No custody, no stuck funds. → See: _CLOB + AMM Hybrid_, _Router Integration_.

### S

**Sequencer** The L2 component that orders transactions. Chainlink monitors sequencer uptime for the oracle — it does not resolve during the grace period after an outage.

**SIWE (Sign-In With Ethereum — EIP-4361)** A standard for web sign-in using a wallet. The user signs a message off-chain, the backend verifies the ECDSA signature, and a session is created (HTTPOnly cookie valid for 7 days). → See: _Wallet Setup_.

**Slippage** The difference between the previewed price and the actual execution price. The default tolerance is 0.5%. If exceeded, the transaction reverts and no funds are lost (only gas is consumed).

**Smart account (ERC-4337)** A contract wallet supporting batch transactions, paymaster, and passkey. PrediX uses Kernel v3 smart accounts with a passkey validator. The opposite of an EOA.

**Split** Mints 1 YES + 1 NO from 1 USDC. The reverse of Merge. Used for market making or arbitrage. Zero-fee, zero-slippage. → See: _Split & Merge_.

**Stake** Lock PRX into the vault → receive stkPRX (non-transferable) + a share of USDC fee yield. → See: _Real Yield Staking_.

**stkPRX** A non-transferable token minted when staking PRX. Represents a claim on the protocol's USDC fee share.

**Streak** A chain of consecutive actions — win streak, daily login streak. Earns badges + bonus rewards. Missing a single day resets it to 0.

### T

**Taker** A user who fills a market order on the CLOB (matching against a resting maker order).

**TBA (To Be Announced)** A placeholder for information not yet published (dates, addresses, parameters).

**Tick (CLOB)** A discrete price level in the order book. PrediX has 99 ticks from $0.01 to $0.99, with a tick size of $0.01. Prices must be multiples of $0.01 (0.455 is rejected or rounded).

**Timelock** A contract that delays the execution of admin actions (48h on PrediX). Diamond and Hook upgrades both pass through the timelock — preventing an admin rugpull. `timelockDuration` is monotonic (can only increase, minimum 48h). → See: _Security & Timelock_.

**TVL (Total Value Locked)** The total USDC locked in the protocol (market collateral + LP + staker).

### U

**UMA oracle** A permissionless oracle with a 48h dispute window plus a DVM (Data Verification Mechanism). Planned for Phase 2. Bond = `max(min_bond, min(tvl × 0.5%, max_bond))`, ranging $500–$50,000.

**Unichain** An L2 EVM chain built by Uniswap Labs. PrediX is currently deployed on Unichain Sepolia testnet (chain 1301); mainnet (chain 130) launches after external audit.

**USDC** USD Coin — a stablecoin pegged 1:1 to USD, with 6 decimals. PrediX uses USDC as the sole collateral for every market. Encoding: 1 USDC = 1,000,000 (6 decimals).

**UserOp (User Operation)** A pseudo-transaction in ERC-4337. Smart accounts submit UserOps via a bundler instead of standard transactions.

### V

**vePRX (vote-escrowed PRX)** PRX locked to gain governance weight + a yield boost. Non-transferable, with weight that decays linearly over time. Formula: `vePRX_weight = PRX_locked × (remaining_lock_time / max_lock_time)`, where max\_lock\_time = 4 years. → See: _vePRX & Gauge Voting_.

**VRF (Verifiable Random Function)** A verifiable on-chain random number. PrediX uses Chainlink VRF for reward boxes.

### W

**Webhook** An HTTP callback that pushes events to a URL you configure. Bots listen via webhooks instead of polling.

### Y

**YES / NO** The outcome tokens of a market. A correct YES = $1, an incorrect YES = $0. Price relationship: `YES_price + NO_price = $1` (always, enforced by the Split/Merge mechanism). → See: _Outcome Token_.

***

> **Can't find a term?** Ask in Discord `#help-desk`, or check the related concept page under _Core Concepts_.

***

### A

#### AA - Account Abstraction

Account Abstraction (AA) is a standard defined by ERC-4337 that allows smart contracts to act as wallets — replacing the traditional private key model.

PrediX uses **Kernel v3 smart accounts** with a **passkey validator**, meaning users sign transactions with biometrics (Face ID, Touch ID) instead of a seed phrase. Gas fees are covered automatically via a **paymaster**, so users never need to hold ETH to interact with the protocol.

> **Why it matters:** AA removes the two biggest barriers to onboarding — managing private keys and buying ETH for gas. On PrediX, new users can start trading immediately after biometric setup.

***

#### AMM - Automated Market Maker

An AMM is a type of decentralized exchange that uses a mathematical formula to price assets automatically, rather than relying on a traditional order book of buyers and sellers.

On PrediX, the AMM layer is powered by **Uniswap v4**, which manages liquidity pools for **YES-USDC** and **NO-USDC** token pairs. When no matching limit order exists on the CLOB, trades are routed to the AMM.

> **Example:** If you want to buy YES tokens at market price without waiting for a seller, the AMM fills your order instantly from the liquidity pool.

***

#### Arbitrage

Arbitrage is a trading strategy that exploits price discrepancies to generate a risk-free profit.

In prediction markets, a classic arbitrage opportunity arises when the sum of YES and NO prices exceeds $1. Since 1 YES + 1 NO always equals $1 USDC at settlement, a trader can:

1. Buy YES and NO separately at a combined price above $1
2. Merge them back into USDC via the **Merge** function
3. Pocket the difference

> **Example:** YES = $0.60, NO = $0.55 → combined cost = $1.15 → merge → receive $1.00 → profit $0.15 per pair (before fees).

***

### B

#### Badge

A Badge is a non-transferable NFT awarded to users who reach specific milestones on PrediX — such as hitting a trading volume threshold, maintaining a winning streak, or providing liquidity consistently.

Badges are displayed on your public profile and the leaderboard, serving as verifiable proof of your activity and performance history.

***

#### Bond

A Bond is collateral you stake to perform certain protocol actions that require accountability.

PrediX uses bonds in two contexts:

* **Oracle resolution (UMA):** A reporter stakes a bond to propose the outcome of a market. If the outcome is disputed and proven incorrect, the bond is slashed.
* **Market creation (Phase 3):** Creating a new market will require a bond to prevent spam and low-quality markets.

Bonds act as the protocol's economic security layer — honest actors get their bond back; bad actors lose it.

***

#### Bridge

A Bridge transfers tokens between different blockchains. If you hold USDC or ETH on Ethereum mainnet and want to trade on PrediX (which runs on Unichain), you'll need to bridge your assets first.

PrediX integrates three bridge providers:

| Bridge        | Notes                        |
| ------------- | ---------------------------- |
| **Across**    | Fast, optimistic bridge      |
| **Stargate**  | Omnichain liquidity protocol |
| **LayerZero** | Cross-chain messaging layer  |

***

#### Bundler

A Bundler is an off-chain service in the ERC-4337 system. It collects **UserOps** (pseudo-transactions from smart accounts), batches them together, and submits them to the blockchain as a single standard transaction.

PrediX uses the **Pimlico bundler** to process smart account actions reliably and efficiently.

***

### C

#### CLOB - Central Limit Order Book

A CLOB is an on-chain order book where buyers and sellers post limit orders at specific prices, and trades execute when a matching order exists.

PrediX's exchange (**PrediXExchange**) features a CLOB with **99 price ticks** ranging from $0.01 to $0.99 in $0.01 increments. This gives traders precise control over their entry and exit prices.

Three matching types are supported:

* **Complementary:** YES order matched with another YES order
* **Mint:** Two BUY orders whose combined value reaches $1 → mints new YES + NO tokens
* **Merge:** Two SELL orders whose combined value reaches $0 → merges and returns USDC

***

#### Cliff

A Cliff is a mandatory waiting period at the beginning of a vesting schedule during which no tokens are released. Only after the cliff period ends do tokens begin to vest.

PRX vesting cliffs by allocation:

| Allocation           | Cliff     |
| -------------------- | --------- |
| Investors            | 6 months  |
| Marketing & Partners | 3 months  |
| Team & Advisors      | 12 months |
| Treasury / DAO       | 6 months  |

***

#### Concentrated Liquidity

Concentrated liquidity is a feature of Uniswap v4 that allows LPs to provide liquidity within a **specific price range**, rather than spreading it evenly across all possible prices ($0.01–$0.99).

**Benefit:** Your capital is used more efficiently, generating higher fees when the market trades within your chosen range.

**Risk:** If the price moves outside your range, you stop earning fees and may experience higher **impermanent loss**.

> **Example:** If you believe a YES token will trade between $0.40 and $0.70, you can concentrate your liquidity there and earn more fees than a full-range LP.

***

### D

#### Diamond - EIP-2535

The Diamond pattern (EIP-2535) is a smart contract architecture that splits a single contract's logic across multiple **facets** (sub-contracts), all accessed via one proxy address.

PrediX uses the Diamond pattern for two reasons:

1. To exceed Ethereum's 24KB bytecode size limit on individual contracts
2. To allow individual modules to be upgraded independently without redeploying the entire protocol

All upgrades go through a **48-hour timelock** to prevent unauthorized changes.

***

### E

#### endTime

`endTime` is the on-chain timestamp at which a market **closes for trading**. After this point, no new orders can be placed or filled.

Once `endTime` is reached, the oracle resolution window opens — the designated oracle will report the real-world outcome and resolve the market.

***

#### EOA - Externally Owned Account

An EOA is a standard crypto wallet controlled by a private key — the type used by MetaMask, Ledger, and most traditional wallets.

EOAs contrast with **Smart Accounts**: they cannot batch transactions, cannot use paymasters natively, and require the user to manage seed phrases. PrediX supports both account types, but recommends smart accounts for the best user experience.

> **Note:** EOA users can still benefit from PrediX's gas sponsorship program via an off-chain rebate mechanism.

***

#### Event (Multi-Outcome)

A multi-outcome Event is a container that groups multiple related markets where **exactly one outcome will resolve YES**.

> **Example:** "FIFA World Cup 2026 Winner" is an Event with 48 child markets — one per team. Only one team will win, so only one market resolves YES = $1; all others resolve NO = $0.

This structure lets traders take positions across competing outcomes within the same event.

***

### F

#### Faucet

The Faucet is a testnet tool that dispenses free test tokens so developers and users can explore PrediX without using real assets.

**Testnet faucet allocation (per wallet, per 24 hours):**

* 0.0005 ETH
* 10,000 USDC

***

### G

#### Gauge Voting

Gauge voting is the mechanism by which **vePRX holders** direct **LP subsidy rewards** to specific prediction markets.

How it works:

1. Lock PRX → receive vePRX
2. Use vePRX to vote for markets you want to incentivize
3. Markets with more votes attract higher LP rewards
4. More rewards → deeper liquidity → better prices for traders

This creates a self-reinforcing cycle where popular, high-quality markets receive more liquidity.

***

### H

#### Hook - Uniswap v4

A Hook is a smart contract that plugs into a Uniswap v4 liquidity pool and executes custom logic at specific points in the trade lifecycle (e.g., `beforeSwap`, `beforeAddLiquidity`).

PrediX's custom Hook performs **anti-sandwich identity verification** — preventing MEV bots from inserting transactions around user trades to extract value.

***

### I

#### Impermanent Loss (IL)

Impermanent Loss is the opportunity cost LPs experience when the price ratio of their deposited tokens changes compared to simply holding those tokens.

> **Example:** You deposit USDC + YES tokens into an AMM pool. If YES price rises sharply, the AMM automatically rebalances your position — you end up with fewer YES tokens than if you had just held them. The "loss" is the difference between your LP position value and what you would have had by holding.

IL is called "impermanent" because it reverses if prices return to their original ratio. It is partially or fully offset by trading fees earned.

***

#### Integrity Bonds

Integrity Bonds are a cryptoeconomic security mechanism where users stake PRX as collateral to create markets or report oracle outcomes.

* If the action is honest and correct → bond is returned
* If the action is fraudulent or incorrect → bond is **slashed** (burned or redistributed)

This replaces centralized gatekeeping with economic accountability — anyone can participate, but bad actors pay a real cost.

***

#### Invariant

An Invariant is a hard-coded rule enforced on-chain at all times. If any transaction would violate an invariant, it is automatically reverted.

PrediX has **7 critical invariants (INV-1 through INV-7)** that protect the integrity of markets, liquidity pools, and the settlement process. These cannot be overridden — not even by the admin team.

***

### L

#### LP - Liquidity Provider

An LP supplies assets to an AMM pool, enabling traders to buy and sell without needing a counterparty. In exchange, LPs earn a share of all trading fees generated by that pool.

On PrediX, LPs deposit **USDC paired with YES or NO tokens** into Uniswap v4 pools. Upon providing liquidity, they receive an **LP NFT** representing their position.

***

#### LP NFT

An LP NFT is the token issued by **Uniswap v4's PositionManager** when you provide liquidity. It represents your specific liquidity position — including the price range, token amounts, and accrued fees.

You can hold, transfer, or redeem your LP NFT to withdraw your liquidity and collected fees at any time.

***

### M

#### Maker

A Maker is a trader who places a **limit order** on the CLOB — specifying the exact price at which they want to buy or sell. The order sits in the order book until a Taker fills it.

Makers provide liquidity to the order book and typically pay lower fees than Takers.

***

#### Market

A Market is the fundamental unit of trading on PrediX. Each market represents a single YES/NO question about a real-world event.

Every market has:

* A **question** (e.g., "Will BTC exceed $100K by Dec 31, 2025?")
* A pair of **outcome tokens** (YES and NO)
* An **oracle** to report the result
* An **endTime** after which trading closes

***

#### Match Type (CLOB)

PrediX's CLOB supports three order matching types:

| Type              | Mechanism                                                                      |
| ----------------- | ------------------------------------------------------------------------------ |
| **Complementary** | Matches a YES order with another YES order on the opposite side                |
| **Mint**          | Two BUY orders combine to reach $1 → protocol mints new YES + NO tokens        |
| **Merge**         | Two SELL orders combine to reach $1 or less → protocol merges and returns USDC |

***

#### Merge

Merge is the operation that burns **1 YES token + 1 NO token** and returns **1 USDC**. It is the reverse of Split.

Merge is **fee-free** and is commonly used for:

* Arbitrage (when YES + NO > $1)
* Exiting positions on both sides of a market simultaneously

***

#### Multisig

A Multisig (multi-signature) wallet requires approval from **multiple parties** before executing any transaction — preventing any single person from acting unilaterally.

PrediX operates three multisig configurations:

* **Admin:** 2-of-3 signatures
* **Operator:** 2-of-3 signatures
* **Oracle Reporter:** 2-of-3 signatures

***

### O

#### Oracle

An Oracle is the source that reports real-world event outcomes on-chain, allowing markets to settle correctly.

PrediX supports four oracle types:

| Type          | Description                                     |
| ------------- | ----------------------------------------------- |
| **Manual**    | Admin team reports the outcome directly         |
| **Chainlink** | Automated data feeds for price-based markets    |
| **UMA**       | Permissionless oracle with a 48h dispute window |
| **Committee** | Multi-party committee vote                      |

***

#### Outcome Token

An Outcome Token is an ERC-20 token representing one side of a prediction market — either **YES** or **NO**.

Settlement values:

* Correct prediction → redeems for **$1 USDC**
* Incorrect prediction → redeems for **$0**

Because outcome tokens are standard ERC-20s, they are composable with the broader DeFi ecosystem — they can be used as collateral, lent, or held in any compatible protocol.

***

### P

#### Passkey

A Passkey is a WebAuthn authentication credential stored in your device's **Secure Enclave** (the same chip that protects Face ID and Touch ID data). It replaces a traditional seed phrase for signing transactions.

On PrediX, passkeys are used as the validator for smart accounts. Your private key never leaves your device, and authentication happens via biometrics — no passwords, no seed phrases.

***

#### Paymaster (ERC-4337)

A Paymaster is a smart contract that pays gas fees on behalf of users, removing the need to hold ETH to interact with the blockchain.

PrediX runs a **gas sponsorship program** for eligible users (new accounts, stakers, campaign participants). Eligibility criteria are published before launch and may be updated via governance.

* **Smart account users:** Paymaster covers gas directly
* **EOA users:** Gas costs are refunded off-chain via rebate

***

#### Permit2

Permit2 is a Uniswap contract that allows users to sign a **one-time ERC-20 approval** off-chain, enabling all future token transfers to happen without additional on-chain approve transactions.

The PrediX Router uses Permit2 to enable gasless token approvals, simplifying the trading experience.

***

#### Points

Points are off-chain tracked rewards accumulated through activity on PrediX — including trading volume, prediction accuracy, LP contributions, and referrals.

Points are distributed as **PRX** on a weekly basis. They serve as a bridge between user activity and token rewards, allowing fair distribution without constant on-chain transactions.

***

#### Prediction Market

A Prediction Market is an exchange where participants trade tokens that represent the probability of a real-world event occurring.

The price of a YES token directly reflects the market's collective estimate of the probability of that outcome.

> **Example:** A YES token trading at $0.72 indicates the market believes there is a **72% probability** the event will occur.

PrediX is a prediction market protocol built on Unichain, combining CLOB precision with AMM liquidity.

***

#### PRX

PRX is the native governance and revenue-sharing token of the PrediX protocol.

**Total supply:** 1,000,000,000 PRX (fixed, no additional minting)

PRX holders can:

* **Stake PRX** → receive stkPRX + USDC yield from protocol fees
* **Lock PRX** → receive vePRX for governance voting and LP reward boosting
* **Participate in governance** → vote on protocol parameters, treasury decisions, and upgrades

***

### Q

#### Quadratic Voting

Quadratic Voting is a governance mechanism where voting power equals the **square root of the tokens staked** — significantly reducing the influence of large token holders (whales).

> **Example:** A user with 10,000 vePRX gets √10,000 = **100 votes**, not 10,000.

PrediX introduces Quadratic Voting in **Phase 2** of the protocol roadmap.

***

### R

#### Real Yield (USDC)

Real Yield refers to revenue distributed to PRX stakers in **USDC** — sourced directly from actual protocol trading fees, not from token emissions or inflation.

This model ensures staker rewards are sustainable and backed by genuine protocol activity.

> **PrediX design principle:** "PrediX earns first. PRX captures second. Holders compound third."

***

#### Refund Mode

Refund Mode is an emergency state activated when the oracle fails to resolve a market within the required window.

In Refund Mode:

* Trading is halted
* Users can burn their YES + NO token pairs
* USDC collateral is returned **pro-rata** to all participants

This mechanism ensures users can always recover their capital even if a market cannot be properly settled.

***

#### Reorg (Reorganization)

A Reorg (blockchain reorganization) occurs when competing versions of the blockchain temporarily exist, causing some recently confirmed transactions to be undone and replaced.

PrediX's **Ponder indexer** automatically handles reorgs — detecting chain reversals and updating the protocol's state accordingly without any user action required.

***

#### Router - PrediXRouter

The PrediXRouter is the main entry point for all trade execution on PrediX. It is a **stateless aggregator** that automatically finds the best execution path for every trade across both the CLOB and the AMM.

**Key invariant:** After every call, the Router's own token balance must equal zero. It holds no user funds between transactions.

***

### S

#### Sequencer

The Sequencer is an L2 component responsible for ordering and processing transactions on Unichain before they are finalized on Ethereum.

PrediX uses **Chainlink** to monitor sequencer uptime. If the sequencer goes offline, the oracle pauses market resolution to prevent unfair settlements during periods of network disruption.

***

#### SIWE - Sign-In With Ethereum

SIWE (EIP-4361) is a standard that allows users to authenticate to web applications using their Ethereum wallet — similar to "Sign in with Google," but decentralized.

On PrediX, users sign an off-chain message with their wallet. The backend verifies the signature and issues a session — no passwords or emails required.

***

#### Slippage

Slippage is the difference between the **expected price** of a trade and the **actual price** at which it executes, caused by market movement between the time you submit a trade and when it settles.

PrediX sets a default slippage tolerance of **0.5%**. Users can adjust this in trade settings — tighter tolerance may cause trades to fail in volatile markets; wider tolerance allows more price movement.

***

#### Smart Account (ERC-4337)

A Smart Account is a contract-based wallet that supports advanced features not available to standard EOA wallets:

* **Batch transactions:** Execute multiple actions in one transaction
* **Passkey signing:** Authenticate with biometrics instead of a private key
* **Paymaster integration:** Pay gas fees in USDC or have them sponsored

PrediX uses **Kernel v3 smart accounts** as the recommended wallet type for the best user experience.

***

#### Split

Split is the operation that deposits **1 USDC** and mints **1 YES token + 1 NO token**. It is the reverse of Merge.

Split is used for:

* **Market making:** Creating YES and NO inventory to place orders on both sides
* **Arbitrage:** When you want to hold both outcomes and later sell one at a profit

***

#### Stake

Staking means locking your PRX tokens into the PrediX vault in exchange for **stkPRX** (a non-transferable staking receipt) and a share of the protocol's **USDC fee revenue**.

Staking is the primary way PRX holders capture value from the protocol's growth.

***

#### Streak

A Streak tracks consecutive activity on PrediX — such as daily logins, winning predictions, or consistent LP activity.

Maintaining streaks earns **Badges** and **bonus rewards**, incentivizing regular engagement with the protocol.

***

#### stkPRX

stkPRX is a **non-transferable** token minted when you stake PRX. It represents your claim on the protocol's USDC fee revenue and cannot be sold or transferred — it can only be redeemed by unstaking.

***

#### Supply Contraction

Supply Contraction is a deflationary mechanism built into PrediX's token economy.

**vePRX locks supply** by taking PRX out of circulation. **Buyback-and-burn** uses protocol revenue to repurchase PRX from the open market and permanently destroy it, compressing the circulating supply over time.

This creates **structural deflation** — as the protocol grows, token supply decreases.

***

### T

#### Taker

A Taker is a trader who fills an existing limit order on the CLOB — accepting the price set by a Maker. Takers execute trades immediately and typically pay slightly higher fees than Makers.

***

#### TBA - To Be Announced

TBA is a placeholder used when specific information (contract addresses, launch dates, parameter values) has not yet been officially published.

***

#### Tick (CLOB)

A Tick is a discrete price level in the PrediX order book. PrediX has **99 ticks** ranging from **$0.01 to $0.99** in increments of **$0.01**.

Each tick represents a possible price for YES or NO tokens. Traders place limit orders at specific ticks.

***

#### Timelock

A Timelock is a contract that introduces a mandatory **48-hour delay** between when an admin action is proposed and when it can be executed.

On PrediX, all upgrades to the Diamond contracts and Uniswap v4 Hooks go through the timelock. This gives the community time to review any changes and respond before they take effect — preventing sudden rug pulls or unauthorized modifications.

***

#### TVL - Total Value Locked

TVL measures the total amount of USDC currently locked inside the PrediX protocol across all components:

* Market collateral (USDC backing outstanding YES/NO tokens)
* LP positions in AMM pools
* PRX staking vault

TVL is a key metric for protocol health and adoption.

***

### U

#### UMA Oracle

UMA is a **permissionless oracle** that allows anyone to report market outcomes without relying on a centralized team.

How it works:

1. A reporter proposes an outcome and posts a **bond**
2. A **48-hour dispute window** opens — anyone can challenge the result
3. If disputed, UMA's **Data Verification Mechanism (DVM)** mediates and decides
4. Honest reporters get their bond back; incorrect reporters lose it

PrediX introduces UMA oracle support in **Phase 2**.

***

#### Unichain

Unichain is an Ethereum Layer 2 (L2) blockchain built by Uniswap Labs, designed for high-performance DeFi applications.

PrediX is natively built on Unichain:

* **Testnet:** Unichain Sepolia (Chain ID: 1301) — currently live
* **Mainnet:** Unichain (Chain ID: 130) — launching after external audit completion

***

#### USDC

USDC (USD Coin) is a stablecoin pegged 1:1 to the US Dollar, with 6 decimal places.

**USDC is the sole collateral currency on PrediX.** All markets are denominated in USDC — you buy outcome tokens with USDC, and winning positions settle back to USDC.

***

#### UserOp - User Operation

A UserOp is the equivalent of a transaction for ERC-4337 smart accounts. Instead of sending a standard Ethereum transaction, smart account users create a UserOp that is submitted to a **Bundler**, which batches and processes it on-chain.

***

### V

#### vePRX

vePRX (Vote-Escrowed PRX) is received when you lock PRX for a set period. It represents your governance weight within the protocol.

vePRX is used for:

* **Gauge voting** — directing LP rewards to specific markets
* **Protocol governance** — voting on parameter changes and upgrades
* **Yield boost** — amplifying your rewards as an LP

**Important:** vePRX weight decays over time as your lock period approaches its end, incentivizing long-term alignment.

***

#### VRF - Verifiable Random Function

A VRF generates **provably fair random numbers on-chain** — the result can be verified by anyone but cannot be predicted or manipulated in advance.

PrediX uses **Chainlink VRF** to power reward boxes and any randomized reward distributions, ensuring fairness and transparency.

***

### W

#### Webhook

A Webhook is an HTTP callback mechanism that pushes real-time event data to a URL you configure, without requiring constant polling.

On PrediX, bots and automated systems can listen to protocol events (new markets, settlements, price movements) via webhooks instead of continuously querying the blockchain.

***

### Y

#### YES / NO

YES and NO are the two **Outcome Tokens** for every prediction market on PrediX.

| Token   | Resolves to | Conditions               |
| ------- | ----------- | ------------------------ |
| **YES** | $1 USDC     | The event occurs         |
| **YES** | $0 USDC     | The event does not occur |
| **NO**  | $1 USDC     | The event does not occur |
| **NO**  | $0 USDC     | The event occurs         |

The current trading price of a YES token represents the market's implied probability of the event occurring.

> **Example:** YES trading at $0.35 → market implies a **35% chance** the event occurs.

