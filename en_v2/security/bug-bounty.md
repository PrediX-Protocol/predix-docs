# Bug Bounty

PrediX operates a responsible disclosure program for all deployed smart contracts.

## Scope

All contracts deployed on Unichain Sepolia Testnet:

| Contract | Address |
|----------|---------|
| Diamond | `0xF38a265E6e4F57D000a1CC08004da5B4A380B08A` |
| Hook | `0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0` |
| Exchange | `0xa202abCb2A358c0862B2dA76b553398339F2C638` |
| Router | `0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7` |
| Oracle | `0x699A8C74663b1C852E195b2ffa00D5965E992Cf3` |

## Severity Levels & Rewards

| Severity | Description | Reward |
|----------|-------------|--------|
| **Critical** | Loss of funds, unauthorized minting, oracle manipulation | Up to $50,000 |
| **High** | Griefing attacks, permanent DoS, access control bypass | Up to $10,000 |
| **Medium** | Temporary DoS, incorrect fee calculation, state inconsistency | Up to $5,000 |
| **Low** | Gas inefficiency, minor view function errors | Up to $1,000 |

## How to Report

1. **Email:** security@predix.io
2. **Include:** Description, reproduction steps, impact assessment, suggested fix
3. **Do NOT** publicly disclose before fix is deployed
4. **Do NOT** exploit the vulnerability on testnet or mainnet

## Responsible Disclosure Policy

- We acknowledge reports within **48 hours**
- We aim to fix critical issues within **7 days**
- Researchers are credited (with permission) in our security disclosures
- No legal action against good-faith researchers

## Out of Scope

- Frontend/UI bugs (report separately)
- Third-party dependencies (Uniswap v4, OpenZeppelin)
- Issues already known (see [Security](../contracts/security.md) for audit report)
- Social engineering attacks

## Next Steps

- [Security](../contracts/security.md) — audit results and safety architecture
- [Contracts Overview](../contracts/overview.md) — all contract addresses
