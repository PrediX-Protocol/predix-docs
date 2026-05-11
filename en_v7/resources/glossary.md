# Glossary

Core terminology sorted alphabetically. Brief definition + example.

### AA (Account Abstraction)

ERC-4337 standard for smart contract wallets. PrediX uses Kernel v3 smart accounts with a **passkey validator** (biometric signing). Smart accounts pay gas via paymaster — PrediX has a gas sponsorship program for eligible users (applies to both account types, see [Paymaster](#paymaster-erc-4337)).

### AMM (Automated Market Maker)

Liquidity pool using a mathematical formula for swap pricing. PrediX uses Uniswap v4 for YES-USDC and NO-USDC pools.

### Arbitrage

Risk-free trade based on price discrepancies. Example: YES + NO > $1 -> split USDC, sell both.

### Badge

NFT earned upon reaching a milestone (volume, accuracy, streak). Displayed on profile + leaderboard.

### Bond

Staked collateral to propose an oracle resolution (UMA) or create a market (Phase 3). Slashed if incorrect.

### Bridge

Transfers tokens between chains. PrediX integrates Across, Stargate, LayerZero.

### Bundler

ERC-4337 service that collects UserOps and submits them on-chain. PrediX uses the Pimlico bundler.

### CLOB (Central Limit Order Book)

On-chain order book. Makers post prices, takers match. PrediX `PrediXExchange` has 99 ticks ($0.01-$0.99).

### Cliff

Period during which no tokens are received in a vesting schedule. PRX team allocation has a 12-month cliff.

### Concentrated liquidity

LP provides liquidity within a narrow price range (vs full range $0.01-$0.99). Higher earnings + higher IL risk.

### Diamond (EIP-2535)

Proxy pattern that allows a single contract to be divided into multiple facets. PrediX uses it to exceed the 24KB bytecode limit + upgrade individual modules.

### EOA (Externally Owned Account)

Traditional wallet controlled by a private key (MetaMask, Ledger). Contrasts with smart account.

### endTime

The point at which a market closes for trading. After endTime, the oracle window opens.

### Event (multi-outcome)

Container of N child markets that are mutually exclusive — exactly 1 member resolves YES = true. Example: "FIFA WC 2026 Winner" with 48 teams.

### Faucet

Dispenser providing free test tokens for testnet users. PrediX faucet: 0.0005 ETH + 10k USDC / 24h / wallet.

### Gauge voting

Mechanism where vePRX votes allocate LP subsidy to markets. Pools with more votes = LPs earn more.

### Hook (Uniswap v4)

Smart contract that plugs into a v4 pool with callbacks (`beforeSwap`, `beforeAddLiquidity`...). The PrediX Hook applies anti-sandwich identity verification.

### Impermanent loss (IL)

LP loss vs holding due to AMM rebalancing when prices move. Offset by earned fees.

### Invariant

Hard-enforced on-chain rule. PrediX has 7 critical invariants (INV-1 through INV-7).

### LP (Liquidity Provider)

Provides liquidity to an AMM pool, receives an LP NFT, and earns swap fees. PrediX LPs supply USDC + YES/NO.

### LP NFT

Token representing an LP position on Uniswap v4 (PositionManager).

### Maker

User who places a limit order awaiting matching on the CLOB.

### Market

The fundamental trading unit — a YES/NO question + outcome token pair + oracle + endTime.

### Match type (CLOB)

3 matching types on Exchange: complementary (YES<->YES), mint (BUY+BUY >= $1), merge (SELL+SELL <= $1).

### Merge

Burns 1 YES + 1 NO -> returns 1 USDC. The reverse of split. Fee-free, used for arbitrage or exiting multi-directional positions.

### Multisig

Wallet requiring N of M signatures to execute. PrediX uses 2/3 admin + 2/3 operator + 2/3 oracle reporter.

### Oracle

Source that reports real-world event outcomes on-chain. PrediX supports Manual, Chainlink, UMA, Committee.

### Outcome token

ERC-20 representing one side of a market (YES or NO). 1 YES correct = $1, 1 YES incorrect = $0. Composable with DeFi.

### Passkey

WebAuthn authentication standard using biometrics (Touch ID, Face ID). Private key lives in the Secure Enclave.

### Paymaster (ERC-4337)

Intermediary contract that pays gas for UserOps on behalf of the user. Smart account users can pay gas via paymaster in multiple ways (USDC, sponsorship, etc). PrediX has a **gas sponsorship program** for eligible users — **applies to both account types** (smart account: paymaster covers directly; EOA: off-chain rebate/refund). Eligibility criteria (new user, staker, campaign event) will be published pre-launch and may change via governance vote.

### Permit2

Uniswap contract enabling a one-time ERC-20 approve + offline signing for subsequent txs. The PrediX Router uses Permit2 for gasless approvals.

### Points

Off-chain tracked activity rewards (trade volume, accuracy, LP, ...). Convert to PRX at weekly distribution.

### Prediction market

Exchange for tokens reflecting event probabilities. YES price = $0.68 -> the market prices a 68% probability of occurrence.

### PRX

Governance + revenue-sharing token of PrediX. Total supply 1B.

### Quadratic voting

Voting mechanism where power = sqrt(stake) — reduces whale dominance. Phase 2 of PrediX.

### Refund mode

Emergency state when the oracle fails. Users burn YES+NO pairs -> receive USDC pro-rata.

### Reorg (reorganization)

Blockchain reorganization due to a fork. The Ponder indexer handles this automatically.

### Router (PrediXRouter)

Stateless aggregator — single entry for all swaps. Automatically splits between CLOB + AMM. Invariant: `balanceOf == 0` post-call.

### Sequencer

L2 component that sequences transactions. Chainlink monitors sequencer uptime for the oracle.

### SIWE (Sign-In With Ethereum)

EIP-4361 standard for web sign-in using a wallet. User signs a message off-chain, BE verifies, mints a session.

### Slippage

Difference between the previewed price and the actual execution price. Default tolerance 0.5%.

### Smart account (ERC-4337)

Contract wallet supporting batch txs, paymaster, passkey. PrediX uses Kernel v3 smart accounts with a passkey validator. Contrasts with EOA (externally-owned account) such as standard MetaMask.

### Split

Mints 1 YES + 1 NO from 1 USDC. The reverse of merge. Used for market making or arbitrage.

### Stake

Lock PRX into vault -> receive stkPRX (non-transferable) + USDC yield share.

### Streak

Chain of consecutive actions. Win streak, daily login streak — earn badges + bonus rewards.

### stkPRX

Non-transferable token minted when staking PRX. Represents a claim on the protocol's USDC fee share.

### Taker

User who fills market orders on the CLOB.

### Tick (CLOB)

Discrete price level in the order book. PrediX has 99 ticks $0.01-$0.99, tick size $0.01.

### Timelock

Contract that delays execution of admin actions (48h on PrediX). Diamond + Hook upgrades go through timelock — prevents admin rugpull.

### TVL (Total Value Locked)

Total USDC locked in the protocol (market collateral + LP + staker).

### TBA (To Be Announced)

Placeholder for information not yet published (dates, addresses, parameters).

### UMA oracle

Permissionless oracle with a 48h dispute window + DVM (Data Verification Mechanism). Phase 2 of PrediX.

### Unichain

L2 EVM chain built by Uniswap Labs. PrediX is currently deployed on **Unichain Sepolia testnet** (chain `1301`); **mainnet** (chain `130`) will launch after external audit completion.

### USDC

USD Coin, stablecoin pegged 1:1 to USD, 6 decimals. PrediX uses USDC as the sole collateral for all markets.

### UserOp (User Operation)

Pseudo-transaction in ERC-4337. Smart accounts submit UserOps via a bundler instead of standard transactions.

### vePRX

Vote-escrowed PRX. Lock PRX -> receive vePRX (governance weight + yield boost). Weight decays over time.

### VRF (Verifiable Random Function)

Verifiable on-chain random number. PrediX uses Chainlink VRF for reward boxes.

### Webhook

HTTP callback that pushes events to a URL you configure. Bots listen via webhooks instead of polling.

### YES / NO

Outcome tokens for a market. See [Outcome tokens](../concepts/outcome-tokens.md).
