---
description: Responsible disclosure and bug bounty program
---

# Bug Bounty Program

## Scope

All deployed smart contracts on Unichain Sepolia:

| Contract | Address |
| -------- | ------- |
| Diamond | `0xF38a265E6e4F57D000a1CC08004da5B4A380B08A` |
| Hook | `0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0` |
| Exchange | `0xa202abCb2A358c0862B2dA76b553398339F2C638` |
| Router | `0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7` |
| Oracle | `0x699A8C74663b1C852E195b2ffa00D5965E992Cf3` |

## Severity Levels

| Severity | Description | Reward |
| -------- | ----------- | ------ |
| **Critical** | Loss of funds, bypass access control | Up to $50,000 |
| **High** | Significant protocol disruption | Up to $10,000 |
| **Medium** | Limited impact bugs | Up to $2,000 |
| **Low** | Minor issues, gas optimizations | Up to $500 |

## How to Report

1. **DO NOT** disclose publicly
2. Email: [security@predix.io]
3. Include: description, reproduction steps, impact assessment
4. Expected response: within 48 hours

## Responsible Disclosure Policy

- Allow 90 days for fix before public disclosure
- Do not exploit vulnerabilities beyond proof-of-concept
- Do not access or modify other users' data
- Testnet exploitation is acceptable for demonstration

---

**Next**: [Security Overview](overview.md) · [Contract Safety](../contracts/safety.md)
