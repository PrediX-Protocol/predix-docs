# Security

Comprehensive security architecture covering access control, safety caps, emergency mechanisms, and audit results.

## Access Control — 4 Roles

| Role | ID | Permissions |
|------|----|-------------|
| `DEFAULT_ADMIN` | 0 | Grant/revoke roles, `diamondCut`, ownership |
| `ADMIN` | 1 | Create markets/categories, approve oracles, set caps, resolve categories |
| `OPERATOR` | 2 | Emergency resolve (7d delay), enable refund mode |
| `PAUSER` | 3 | Pause/unpause individual facets or entire protocol |

### Role Matrix

| Function | DEFAULT_ADMIN | ADMIN | OPERATOR | PAUSER | PUBLIC |
|----------|:---:|:---:|:---:|:---:|:---:|
| diamondCut | ✅ | | | | |
| createMarket | | ✅ | | | |
| setApprovedOracle | | ✅ | | | |
| setSafetyCaps | | ✅ | | | |
| resolveCategory | | ✅ | | | |
| emergencyResolve | | | ✅ | | |
| enableRefundMode | | | ✅ | | |
| pause / unpause | | | | ✅ | |
| splitPosition | | | | | ✅ |
| resolveMarket | | | | | ✅ |
| redeemMarketTokens | | | | | ✅ |
| placeOrder | | | | | ✅ |

### Ownership Protection

- **Two-step transfer:** `transferOwnership` → `acceptOwnership` prevents accidental transfers
- **Last-admin protection:** Cannot remove the last `DEFAULT_ADMIN`

## Safety Caps

Three configurable caps limit systemic risk:

| Cap | Function | Purpose |
|-----|----------|---------|
| TVL Cap | `setTvlCap(uint256)` | Maximum total USDC locked in protocol |
| Per-Trade Cap | `setPerTradeCap(uint256)` | Maximum single trade size |
| Per-Market Cap | `setPerMarketCap(uint256)` | Maximum collateral per market |

## Emergency Mechanisms

| Mechanism | Trigger | Delay | Role |
|-----------|---------|-------|------|
| **Pause** | Security incident | Immediate | PAUSER |
| **Emergency Resolve** | Oracle failure | 7 days after endTime | OPERATOR |
| **Refund Mode** | Unresolvable market | Immediate | OPERATOR |

> ⚠️ Refund mode is irreversible. Once enabled, the market cannot be resolved normally.

## Anti-MEV Protection

- **200ms Flashblocks:** Unichain's ultra-fast blocks minimize front-running window
- **TEE ordering:** Trusted Execution Environment prevents malicious reordering
- **Dynamic fees:** 5% fee near expiry makes sandwich attacks unprofitable

## Reentrancy Guards

Three types of reentrancy protection across all external functions:

1. **OpenZeppelin ReentrancyGuard** on Diamond facets
2. **Uniswap v4 lock pattern** on Hook callbacks
3. **Custom guards** on Exchange order matching

## Audit Summary

| Metric | Result |
|--------|--------|
| Unit + integration tests | 264 passed |
| Independent AI audits | 3 (cross-verified) |
| Slither static analysis | 0 real issues |
| Fuzz testing | 14 campaigns × 10,000 runs |
| On-chain E2E tests | 31 on Unichain Sepolia |
| Bugs found & fixed | 22 |
| OWASP score | 10/10 |

### Bugs Found & Fixed (Highlights)

- Reentrancy in multi-step order matching → added custom guard
- Price overflow at boundary ticks → added bounds checking
- Missing access control on admin setters → added role checks
- Flash loan attack vector on split → added same-block protection

For responsible disclosure of new vulnerabilities, see [Bug Bounty](../security/bug-bounty.md).

## Next Steps

- [Bug Bounty](../security/bug-bounty.md) — report vulnerabilities
- [Diamond](diamond.md) — upgrade process
- [Oracle](oracle.md) — oracle architecture
