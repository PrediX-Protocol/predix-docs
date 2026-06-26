# Disclaimers & Risks

### Figures Are Illustrative

All figures, examples, fee rates, prices, returns, and liquidity numbers across this documentation are provided for reference and illustration only. They do not constitute financial, investment, legal, or tax advice, and are not guarantees of any outcome or return.

Protocol parameters — including AMM swap fees, CLOB fees, market-creation fees, and the dynamic fee schedule — are configurable and may change at any time without prior notice. Always verify current parameters on-chain before transacting. Nothing here is a commitment to deliver any feature, parameter, or economic result.

PrediX is an experimental, decentralized, non-custodial protocol for predictive markets. Participation in any PrediX product - including trading, liquidity provisioning, staking, governance, and any product feature described in this documentation - involves material risk.

This page outlines the categories of risk that users, liquidity providers, token holders, and integrators should understand before interacting with the protocol. The list is not exhaustive. Users are solely responsible for their own due diligence and the consequences of their actions on the protocol.

No content in this documentation constitutes financial, legal, tax, or investment advice.

### 1. General Disclaimer

The PrediX documentation, including this page, is provided for informational purposes only. Nothing on this site or within any related materials (whitepapers, social channels, presentations, community discussions) should be construed as:

* A solicitation to buy, sell, or hold any asset
* Financial, legal, tax, accounting, or investment advice
* A guarantee of future performance, returns, or token value
* A representation that any product, feature, or roadmap milestone will be delivered on a specific date

Forward-looking statements - including roadmap items, projected metrics, and feature timelines - are subject to change without notice and may be modified or removed entirely based on technical, market, or governance considerations.

### 2. Regulatory & Jurisdictional Risk

Prediction markets occupy an evolving and uncertain regulatory landscape. The legal status of on-chain prediction markets, outcome tokens, governance tokens, and adjacent financial products varies significantly across jurisdictions and may change over time.

Users acknowledge and accept that:

* It is the user's sole responsibility to determine whether their interaction with PrediX is lawful in their jurisdiction.
* PrediX may restrict access from certain regions to comply with applicable regulations. Users must not attempt to circumvent geographic restrictions.
* Some jurisdictions may classify prediction market participation as gambling, securities trading, or other regulated activity.
* Tax treatment of trading profits, staking yield, airdrops, and token sales is the user's responsibility to determine and report.

PrediX does not provide regulatory guidance. If you are unsure whether participation is permissible in your jurisdiction, consult qualified legal counsel before interacting with the protocol.

### 3. Smart Contract Risk

PrediX is composed of multiple interconnected smart contracts deployed on Unichain, integrated with Uniswap v4 hooks, oracle adapters, and routing infrastructure.

Despite extensive internal review, external audits, and active bug bounties, smart contracts may contain:

* Undiscovered vulnerabilities that allow loss or theft of user funds
* Logical errors in fee calculation, position settlement, or yield distribution
* Composability failures when interacting with third-party protocols (Uniswap v4, oracles, bridges)
* Upgrade and timelock vectors that introduce risk during protocol upgrades

Mitigations include independent audits by qualified firms, an open bug bounty program, a 48-hour timelock on critical contract upgrades, an insurance fund for partial reimbursement after exploits (subject to DAO vote), and full open-source contract code for community review.

No mitigation eliminates smart contract risk entirely. Users should only deploy capital they can afford to lose.

### 4. Market & Trading Risk

Prediction markets are zero-sum at resolution. Outcome tokens of the losing side resolve to $0, resulting in a total loss of the capital allocated to that side.

Specific trading risks include:

* Total capital loss. Holding YES tokens on a market that resolves NO (or vice versa) results in 100% loss of the position.
* Adverse price movement. Outcome token prices can move sharply against a position prior to resolution.
* Liquidity risk. Long-tail markets may have shallow liquidity, leading to high slippage on entry and exit.
* Time decay risk. Positions may become illiquid as a market approaches resolution.
* Volatility risk. Markets reacting to breaking news, oracle updates, or correlated events may experience rapid repricing.
* Counterparty informational asymmetry. Other participants may possess superior information.

The user is solely responsible for evaluating the probability, payoff, and risk profile of any market before entering a position.

### 5. Liquidity Provider Risk

Providing liquidity on PrediX exposes users to risks beyond standard AMM liquidity provision:

#### Impermanent Loss at Resolution

When a prediction market resolves, the AMM pool effectively settles to 0 or 1 for the YES/USDC pair. LPs holding positions through resolution experience an asymmetric outcome that is structurally different from impermanent loss in standard token pairs. This is a defining risk of prediction market LPing and cannot be fully hedged through traditional means.

#### Toxic Flow Near Expiry

As markets approach resolution, informed traders gain a disproportionate advantage over passive liquidity. PrediX mitigates this through dynamic fee scaling (up to 5% in the final 24 hours), but mitigation is not elimination.

#### Concentrated Position Risk

LPs providing liquidity in a narrow price range may have their positions fully converted to one side of the pair under volatile conditions.

#### Yield Variability

LP yield depends on trading volume in the specific pool. Volume can decline sharply between events, reducing realized fee income.

LPs should review the seven LP monitoring metrics (fee earned, current IL, net P\&L, break-even days, pool share, days to expiry, fee APR) in the Liquidity Manager before and during position management.

### 6. Oracle & Resolution Risk

Market resolution depends on oracle data. PrediX supports a modular oracle architecture (Manual, Chainlink Automation, UMA), each with distinct risk profiles:

* Manual Oracle (Phase 1): Resolved by a designated multisig. Vulnerable to operator error or unavailability.
* Chainlink Automation: Depends on the continued operation and integrity of Chainlink data feeds.
* UMA Oracle (Phase 2): Permissionless with a 48-hour dispute window. Susceptible to game-theoretic disputes during the window.

Possible resolution outcomes include:

* Delayed resolution due to data unavailability or dispute
* Incorrect resolution corrected through dispute escalation
* Refund mode when a market cannot be objectively resolved - collateral is returned to participants pro-rata

Users acknowledge that oracle infrastructure introduces dependencies beyond the protocol's direct control.

### 7. Token (PRX) Risk

The PRX token is subject to risks common to all digital assets, plus several specific to PrediX:

* Price volatility. PRX may experience significant price fluctuations driven by market conditions, sentiment, or macro events.
* Yield variability. Staking yield is denominated in USDC and sourced from protocol fees. Yield decreases if protocol volume declines.
* Lock-up risk. Tokens locked into vePRX cannot be unlocked before expiry without penalty (early-unlock with 50% penalty planned for Phase 2).
* Staking cooldown. Unstaking is subject to a 7-day cooldown during which yield continues to accrue but the principal is not withdrawable.
* Vesting overhang. Scheduled token unlocks across multiple buckets may introduce supply pressure at predictable intervals.
* Buyback effectiveness. The deflationary effect of buyback-and-burn depends on sustained protocol revenue.
* TGE timing uncertainty. Token generation is conditional on performance gates and is not guaranteed by any fixed date.

### 8. Governance Risk

PrediX governance is administered through vePRX voting. Governance risks include:

* Vote concentration. Large vePRX holders may exert disproportionate influence over protocol parameters and treasury spend.
* Bribe-driven voting. vePRX holders may vote in response to bribe incentives rather than long-term protocol health.
* Low participation. Quorum failures or apathy may allow contested proposals to pass with minority support.
* Adverse parameter changes. Governance may adjust fees, emissions, or other parameters in ways that materially affect user outcomes.
* Phase transition risk. Major phase transitions (Bootstrap → Scale → Mature → Dominance) introduce new mechanics that have not yet been validated under live conditions.

### 9. Product-Specific Risks

#### BNPL (Buy Now, Pay Later)

* Liquidation risk. Positions are collateralized by the locked outcome tokens. If the user defaults on an installment, the protocol may sell or liquidate part of the position to recover funds.
* Market value risk. The collateral position may decline in value below the outstanding installment balance, in which case the user remains exposed to the difference.
* Schedule rigidity. Installments must be paid on schedule. Missed payments trigger liquidation procedures defined by the smart contract.

#### Lossless Lottery

* Principal is "near-intact," not guaranteed. The protocol allocates deposits to high-probability YES tokens (typically > 95%). In rare adverse events, residual principal risk exists.
* Yield variability. Jackpot size depends on yield generated and the number of participants. There is no minimum payout guarantee.
* No fixed odds. Lottery odds depend on total deposits, lock duration, and any PRX-based boost multipliers held at draw time.

#### Crowdfunded Market Creation

* Funding shortfall. If a proposed market does not reach its target liquidity within the 7-day window, all backer funds are refunded. No market is launched and no NFT revenue-share token is issued.
* Post-launch market risk. Once launched, the market is subject to standard prediction market risks. Backers' NFT-based revenue share depends on the market generating fee income.

#### Group Buying

* Threshold failure. If the participant or volume threshold is not met within the 24-hour window, users are refunded or executed at standard pricing without the group discount.
* Discount dependency. Group discounts apply only when the entire threshold is met. Partial participation does not trigger partial discounts.

#### Liquidity Manager

* Tool-level abstraction. The Liquidity Manager simplifies AMM liquidity management but does not eliminate underlying AMM risk.
* Alert reliance. Risk alerts (IL > 15%/25%, probability > 75% or < 25%, < 1 day to expiry) are informational. Users remain responsible for position management.

#### Perp Prediction (Phase 3)

* Funding rate exposure. Continuous funding rates may accumulate against open positions.
* PRX margin volatility. Margin is denominated in PRX. PRX price fluctuations directly affect margin sufficiency.
* Liquidation risk. Positions can be liquidated if margin falls below the required threshold.

### 10. Infrastructure & Operational Risk

* Network risk. PrediX operates on Unichain. Network outages, reorganizations, or upgrades may affect protocol availability.
* Bridge risk. USDC bridged via Circle CCTP or third-party bridges depends on the security of those systems.
* Wallet & key management risk. Loss of access to a passkey, smart account, or EOA may result in permanent and unrecoverable loss of funds. Backup mechanisms (cloud sync, hardware key, second device) are the user's responsibility.
* Indexer or RPC downtime. UI and data services may be temporarily unavailable. Protocol settlement continues on-chain regardless.
* Admin actions. Certain protocol functions remain administratively controlled during the bootstrap phase, including pause functionality. All administrative actions are subject to a 48-hour timelock and on-chain verification.

### 11. No Guarantees

PrediX makes no representation or warranty regarding:

* The value of PRX or any outcome token at any point in time
* The continued availability of any feature, market, or product
* The accuracy or completeness of any market description, oracle data, or resolution
* The successful delivery of any roadmap item or phase transition
* The performance of any third-party dependency (bridges, oracles, wallets, exchanges)

All participation is at the user's own risk.

### 12. Acknowledgment

By interacting with PrediX in any capacity - trading, providing liquidity, staking PRX, participating in governance, using BNPL, joining crowdfunded markets, or any other feature - users acknowledge and accept all risks described on this page, as well as any additional risks not explicitly listed.

PrediX, its contributors, and its associated entities disclaim all liability for losses arising from protocol use, to the maximum extent permitted by applicable law.

If you do not accept these risks, do not interact with the protocol.

For questions regarding specific risks, contributors and community moderators are available through official PrediX channels. Always verify channel authenticity - impersonation and phishing attempts are common in DeFi.
