---
description: Role-based access control — 4 roles with granular permissions
---

# Access Control

## Roles

| Role | ID | Purpose |
| ---- | -- | ------- |
| DEFAULT_ADMIN | 0 | Manage all roles |
| ADMIN | 1 | Create markets, config fees, approve oracles |
| OPERATOR | 2 | Emergency resolve, enable refund |
| PAUSER | 3 | Pause the system |

## Permission Matrix

| Function | PUBLIC | ADMIN | OPERATOR | PAUSER | OWNER |
| -------- | ------ | ----- | -------- | ------ | ----- |
| `createMarket` | | ✓ | | | |
| `splitPosition` | ✓ | | | | |
| `mergePositions` | ✓ | | | | |
| `resolveMarket` | ✓ | | | | |
| `redeemMarketTokens` | ✓ | | | | |
| `placeOrder` | ✓ | | | | |
| `buyYes` (Router) | ✓ | | | | |
| `emergencyResolve` | | | ✓ (7d) | | |
| `enableRefundMode` | | | ✓ | | |
| `setTvlCap` | | ✓ | | | |
| `setApprovedOracle` | | ✓ | | | |
| `pauseAll` | | | | ✓ | |
| `diamondCut` | | | | | ✓ |

## Two-Step Ownership Transfer

```
1. Current owner calls transferOwnership(newOwner)
2. newOwner becomes pendingOwner
3. newOwner calls acceptOwnership()
4. Ownership transferred
```

This prevents accidental transfers to wrong addresses.

## Last-Admin Protection

The system prevents revoking the last admin's role, ensuring the protocol always has at least one admin.

---

**Next**: [Safety](safety.md) · [Diamond Contract](diamond.md)
