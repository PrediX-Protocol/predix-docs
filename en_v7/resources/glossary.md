---
description: >-
  Core PrediX terminology, sorted alphabetically. Each entry includes a short
  definition plus an example or a cross-reference to the relevant page. When you
  hit an unfamiliar term anywhere in the docs,
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
