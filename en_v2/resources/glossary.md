# Glossary

## Protocol Terms

| Term | Definition |
|------|-----------|
| **Prediction Market** | A market where participants trade on the outcome of future events |
| **Outcome Token** | ERC-20 token representing a specific outcome (YES or NO) |
| **YES Token** | Pays $1 USDC if the event occurs; $0 otherwise |
| **NO Token** | Pays $1 USDC if the event does not occur; $0 otherwise |
| **Split** | Convert USDC into equal YES + NO tokens (1 USDC → 1 YES + 1 NO) |
| **Merge** | Convert equal YES + NO tokens back into USDC (1 YES + 1 NO → 1 USDC) |
| **Redeem** | Exchange winning tokens for $1 USDC each after market resolution |
| **Category** | A group of related binary markets for multi-outcome events |

## Architecture Terms

| Term | Definition |
|------|-----------|
| **CLOB** | Central Limit Order Book — on-chain order matching engine |
| **AMM** | Automated Market Maker — algorithmic liquidity pool (Uniswap v4) |
| **Smart Router** | Aggregates CLOB and AMM liquidity for best execution |
| **Hook** | Uniswap v4 custom contract that runs logic at pool lifecycle points |
| **Diamond Proxy** | EIP-2535 pattern allowing modular, upgradeable smart contracts |
| **Facet** | A module within a Diamond proxy containing related functions |
| **Oracle** | External contract that determines market outcomes |

## DeFi Terms

| Term | Definition |
|------|-----------|
| **TVL** | Total Value Locked — total USDC collateral in the protocol |
| **Slippage** | Difference between expected and actual execution price |
| **Spread** | Difference between best bid and best ask price |
| **Depth** | Total order volume available at a given price level |
| **IL** | Impermanent Loss — value loss from LP position due to price movement |

## Token Standards

| Term | Definition |
|------|-----------|
| **USDC** | USD Coin — stablecoin used as collateral (6 decimals) |
| **ERC-20** | Standard fungible token interface on EVM chains |
| **ERC-2612** | Permit extension allowing gasless token approvals via signatures |
| **ERC-1155** | Multi-token standard (used by Polymarket, not composable with DeFi) |
