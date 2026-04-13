---
description: Security posture — audits, testing, and safety mechanisms
---

# Security Overview

## Audit Summary

| Metric | Value |
| ------ | ----- |
| Source files | 44 Solidity files, 6,222 lines |
| Unit tests | 264 tests, 0 failures |
| Fuzz tests | 14 tests × 10,000 runs |
| On-chain E2E | 31 tests on Unichain Sepolia |
| Audit reports | 3 independent reviews cross-verified |
| Slither analysis | 0 real security issues |
| OWASP coverage | 10/10 categories (SC Top 10 2026) |
| Security fixes | 22 bugs found and fixed |

## Safety Mechanisms

1. **Safety Caps**: TVL, per-trade, per-market limits (configurable)
2. **Pause**: PAUSER can freeze specific modules or entire system
3. **Emergency Resolve**: OPERATOR can resolve after 7-day delay
4. **Refund Mode**: Proportional refund for problematic markets
5. **Anti-Sandwich**: Hook-level detection (same block, opposite direction)
6. **Reentrancy Guards**: 3 independent guards across contracts
7. **Two-Step Ownership**: Prevents accidental ownership transfer

## Smart Contract Risks

While PrediX has undergone extensive testing and auditing, smart contract risk can never be fully eliminated. Users should:

- Start with small amounts
- Understand the risks of prediction market trading
- Never invest more than they can afford to lose

---

**Next**: [Audit Reports](audit-reports.md) · [Bug Bounty](bug-bounty.md) · [Safety Mechanisms](../contracts/safety.md)
