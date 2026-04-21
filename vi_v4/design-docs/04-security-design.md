---
description: Comprehensive threat model, mitigations, key management, security architecture. Deliverable for audit firms + enterprise security review.
---

# Security Design Document

**Document status**: v1.0 — 2026-04 (Phase 1 testnet security posture)
**Audience**: External audit firms, enterprise security reviewers, bug bounty researchers, regulators
**Companion docs**: [System Architecture](00-system-architecture.md), [Smart Contract Spec](01-smart-contract-spec.md), [Oracle Design](02-oracle-design.md), [Multisig Policy](06-multisig-key-management.md)

---

## 1. Security philosophy

PrediX applies **defense in depth** across 5 layers:

1. **Protocol design** — minimize trust, reduce attack surface (non-custodial, stateless router, ERC-20 tokens, transparent upgrade)
2. **Smart contract implementation** — custom errors, reentrancy guards, fail-loud patterns, type-safe primitives
3. **Operational security** — multisig, timelock, HSM, role separation, incident drill
4. **Observability** — metrics, alerts, audit trail on-chain + off-chain
5. **Community defense** — bug bounty, public dashboards, transparent governance

**Principle**: Every user-facing boundary should assume the other side is adversarial. Validate, verify, fail loud.

## 2. Threat model

### 2.1 Assets protected

| Asset | Value | Protection mechanism |
|---|---|---|
| User collateral (USDC locked as split positions) | All user deposits | Smart contract solvency invariants INV-1, INV-2 |
| Pending CLOB orders collateral | Within orders | INV-2 exchange solvency |
| Redemption payouts | Winning token value | INV-6 resolution monotonic, fee cap INV-4 |
| Protocol treasury | Fee collections | Multisig admin control |
| User identity / privacy | Address activity | On-chain privacy limited — transparent by design |
| Governance state | Upgrade authority | INV NEW-01 (CUT_EXECUTOR isolation) |
| Indexer state | Derived, re-buildable | RPC fallback, finalized commits |
| Session tokens (BE auth) | Short-lived | 24h TTL, HTTPS-only transport |
| Oracle keys | Critical | Hardware wallet, multisig, geographic distribution |

### 2.2 STRIDE analysis

**Spoofing**:
- Attack: Fake contract impersonating PrediX
- Mitigation: Canonical addresses on [deployed-addresses.md](../smart-contracts/deployed-addresses.md), ENS + contract verification on Etherscan, clear brand + UI

**Tampering**:
- Attack: Modify on-chain state (drain collateral, forge resolution)
- Mitigation: Reentrancy guards, role-based access, timelock upgrade, invariant fuzz tests

**Repudiation**:
- Attack: User claims they didn't authorize trade
- Mitigation: On-chain tx + signature — immutable, verifiable by anyone

**Information disclosure**:
- Attack: Read private user data
- Mitigation: On-chain data is public (by design). Off-chain PII (if any) encrypted at rest + in transit.

**Denial of service**:
- Attack: Prevent users from trading / redeeming
- Mitigation: Pause asymmetry (user can always exit), multi-RPC fallback, rate limiting at edge

**Elevation of privilege**:
- Attack: Gain admin privileges unauthorized
- Mitigation: 3/5 multisig, HSM signers, `CUT_EXECUTOR_ROLE` isolation, last-admin protection

### 2.3 Attacker profiles

| Profile | Budget | Skill | Motivation | Key risks |
|---|---|---|---|---|
| **Script kiddie** | < $1K | Low | Learning / random griefing | DDoS, low-value spam |
| **Profit trader** | $10K–$100K | Medium | PnL maximization | Sandwich, front-run, flash-loan arb |
| **Sophisticated hacker** | $100K–$1M | High | Fund drain | Smart contract exploit, admin key theft |
| **Nation-state / APT** | Unlimited | Very high | Political / censorship | 0-day exploit, infrastructure compromise, supply chain |
| **Insider** | Access-based | Variable | Personal profit | Key abuse, governance manipulation |
| **Competitor** | Unlimited | High | Reputation damage | Social engineering, FUD, slow-burn sabotage |

## 3. Attack surfaces & mitigations

### 3.1 Smart contract attack surface

#### 3.1.1 Reentrancy

**Threat**: Malicious contract calls back into PrediX mid-execution, causing inconsistent state.

**Mitigation**:
- `TransientReentrancyGuard` (EIP-1153) on 20+ external functions
- Checks-Effects-Interactions pattern enforced
- External calls ordered LAST in function body
- Fuzz + property tests confirm no reentrancy path

**Verified by**: Audit checklist (SWC-107), invariant tests.

#### 3.1.2 Access control bypass

**Threat**: Unauthorized address calls admin function.

**Mitigation**:
- Role-based access via `AccessControlFacet`
- Every admin function has `onlyRole` modifier FIRST
- Event emission on all admin calls (public audit trail)
- Last-admin protection prevents accidental lockout

**Special case (NEW-01)**: `CUT_EXECUTOR_ROLE` isolated from `DEFAULT_ADMIN_ROLE` — only TimelockController. Admin cannot grant `CUT_EXECUTOR_ROLE` to bypass timelock (verified at deploy: `getRoleMemberCount(CUT_EXECUTOR_ROLE) == 1 && getRoleMember(CUT_EXECUTOR_ROLE, 0) == timelockAddress`).

#### 3.1.3 Oracle manipulation

**Threat**: Bad data → wrong resolution → attacker profits.

**Mitigation** (layered):
- Oracle approval list (Diamond admin controls which oracles are valid)
- Re-verify at resolve (FINAL-D-03): revoke oracle → market can't resolve
- Chainlink: staleness (24h), sequencer uptime, round completeness, price > 0
- ManualOracle: 3/5 multisig admin
- Phase 2 UMA: bonded dispute mechanism

**Accepted residual risk**: Chainlink feed itself being manipulated (rare; Chainlink has its own security). Mitigation: multi-feed in Phase 3.

#### 3.1.4 Router drain

**Threat**: Attacker induces Router to hold funds post-call → withdraw later.

**Mitigation**:
- INV-3: Zero-custody invariant asserted on every public function via `_refundAndAssertZero`
- 83 router tests including fuzz
- Trusted router whitelist in Hook (prevents arbitrary-contract integration)

#### 3.1.5 Sandwich attack

**Threat**: MEV bot sees user swap in mempool, front-runs + back-runs to extract value.

**Mitigation** (Hook identity commit):
- Router commits `msg.sender` to Hook's transient storage BEFORE `poolManager.unlock`
- Hook's `beforeSwap` verifies commit present + matches expected caller
- Non-trusted routers (attacker-deployed) not whitelisted → can't commit → Hook rejects swap
- Transient storage auto-cleared at tx end (no cross-tx leakage)

**Residual**: Attacker still needs Router whitelist to interact with pool — future trusted-router list management is critical.

#### 3.1.6 Storage collision (Diamond)

**Threat**: Facet-A storage overlaps Facet-B → cross-corruption.

**Mitigation**:
- Diamond storage pattern: each module has unique `keccak256("predix.storage.<module>")` slot
- Storage libraries enforce isolation
- Append-only storage (audit checks)
- Integration tests verify no collision on upgrades

#### 3.1.7 Integer overflow / underflow

**Threat**: Arithmetic errors corrupt state.

**Mitigation**:
- Solidity 0.8.30 built-in checked math
- `unchecked` blocks only with comment explaining safety
- `FullMath.mulDiv` for tick math (Uniswap v4)
- Explicit bounds on all fee / price / amount inputs

#### 3.1.8 Griefing via resource exhaustion

**Threat**: Attacker creates N markets, fills orderbook with 50 orders each, etc. — consuming protocol resources.

**Mitigation**:
- `MAX_ORDERS_PER_USER = 50` per market
- `MAX_FILLS = 20` per placeOrder (bounded gas)
- Per-market TVL cap (`perMarketCap`)
- Admin can adjust caps + require creation fee (`marketCreationFeeBps`) if abuse emerges

### 3.2 Off-chain attack surface

#### 3.2.1 RPC compromise

**Threat**: Malicious RPC returns fake state to Indexer or user.

**Mitigation**:
- 3-node RPC fallback (primary + 2 backup)
- Ponder uses `finalized` tag — if RPC lies about `finalized`, Ponder detects reorg when switching RPC
- Long-term: consensus RPC (query N RPCs, majority wins)

**For users**: Frontend uses public RPC but also allows user to override with own RPC (wallet-level trust).

#### 3.2.2 Indexer compromise

**Threat**: Attacker gains access to Indexer infrastructure, feeds false data to BE.

**Mitigation**:
- Indexer state is **derivable** — if compromised, wipe + re-backfill from RPC
- BE validates data schema via zod, catches drift
- Critical paths (trade execution) never rely on Indexer — user talks to Router directly

#### 3.2.3 Backend compromise

**Threat**: Attacker gains BE admin access, modifies Mongo overlays (change market title, hide legitimate markets, etc.).

**Mitigation**:
- Mongo access gated by IP whitelist + credential
- Admin endpoints require SIWE + role check
- All admin writes logged to `admin_audit_logs` collection (append-only)
- On-chain state unaffected — attacker can only modify cosmetic metadata
- FE fallback: if BE compromised, user can read markets directly from Indexer public endpoint + trade direct via wallet

#### 3.2.4 SIWE session hijack

**Threat**: Attacker steals session token, impersonates user.

**Mitigation**:
- Session token stored in `localStorage` (not cookies — no CSRF) or user-controlled secure storage
- 24h TTL
- All sensitive admin endpoints require role check (not just session)
- HTTPS-only
- Revocation: user `logout` or session-expired event → BE invalidates server-side
- Future: bind session to IP subnet (reduce travel risk but can break legit users)

**Residual**: XSS on FE → token leak. Mitigation: CSP, sanitize user-generated content (comments).

#### 3.2.5 DNS / domain takeover

**Threat**: Attacker hijacks `predixpro.io` DNS, redirects users to fake UI to steal keys.

**Mitigation**:
- Registrar 2FA + registry lock (prevents unauthorized transfer)
- CAA DNS records
- Domain renewal auto-pay + expiry alerts
- DNSSEC enabled
- IPFS mirror of frontend (backup if DNS compromised)

#### 3.2.6 Supply chain attack

**Threat**: Compromised npm package (viem, wagmi, etc.) ships malicious code to FE or Indexer.

**Mitigation**:
- Dependabot + Renovate for version tracking
- Lock files committed (`pnpm-lock.yaml`)
- Review high-risk deps (crypto, wallet) quarterly
- Critical deps pinned exact version (viem, wagmi, nestjs pinned)
- npm audit in CI
- Subresource integrity for CDN-loaded scripts (if any)

### 3.3 Operational attack surface

#### 3.3.1 Multisig signer compromise

Chi tiết: [Multisig & Key Management Policy](06-multisig-key-management.md).

**Summary**:
- 3/5 threshold (Phase 1) / 4/7 (Phase 2)
- Hardware wallets (Ledger) mandatory
- Geographic distribution (min 3 continents)
- No shared accounts / keys
- Screen-share prohibited during signing sessions
- Key rotation annually

#### 3.3.2 Insider risk

**Threat**: Team member abuses access.

**Mitigation**:
- Principle of least privilege — role grants are minimal
- No single signer has full admin control (multisig required)
- All admin actions audit-logged on-chain (immutable) + off-chain (Mongo)
- Time-locked upgrades give community 48h window to detect + alert
- Whistleblower channel (anonymous reporting)

#### 3.3.3 Social engineering

**Threat**: Attacker impersonates team member in Discord/TG/email, tricks signer to sign malicious tx.

**Mitigation**:
- Signers verify tx content on hardware wallet screen (not assume multisig UI is honest)
- Multi-channel confirmation for urgent requests (Discord + TG + voice call)
- Known signer handles locked + verified
- Training: "never sign if you didn't initiate the action yourself"
- Quarterly phishing drill

#### 3.3.4 Developer environment compromise

**Threat**: Attacker compromises dev laptop, inserts backdoor in deployment.

**Mitigation**:
- Deployment scripts require separate multisig confirmation (not just dev running locally)
- Contract deployment verified on-chain (bytecode match source)
- CI/CD pipeline runs from locked runner (GitHub Actions with minimal perms)
- Deploy from dedicated hardware wallet (not dev wallet)
- Sign all release tags with GPG

### 3.4 Economic attack surface

#### 3.4.1 Griefing via refund mode abuse

**Threat**: Attacker creates spam markets, triggers refund mode, drains liquidity in+out.

**Mitigation**:
- `createMarket` is ADMIN-only (Phase 1-2; permissionless Phase 4+)
- `enableRefundMode` is OPERATOR_ROLE only
- Creation fee `marketCreationFeeBps` can be enabled if spam emerges

#### 3.4.2 Liquidity drain via routing

**Threat**: Attacker manipulates CLOB + AMM to extract value via Router.

**Mitigation**:
- Router has slippage protection (minOut enforced)
- Deadline parameter prevents stale tx execution
- Quote revert pattern: quotes are simulations, not guarantees — frontend must re-quote before submit
- INV-3 Router zero-custody — no leftover funds to drain
- Exchange MINT/MERGE surplus goes to protocol, not attacker

#### 3.4.3 Flash loan attack on oracle

**Threat**: Flash loan to manipulate price feed momentarily, trigger resolution at favorable price.

**Mitigation**:
- Oracle resolve is NOT instant at `resolveTime` — requires separate `resolve()` call
- Chainlink feeds are TWAP-like for major assets (time-averaged)
- L2 sequencer uptime feed catches brief feed disruptions
- Phase 3: multi-feed + dispute window reduces any single-feed exploit

### 3.5 Regulatory / legal attack surface

#### 3.5.1 Jurisdiction-targeted restrictions

**Threat**: Specific country orders team to block markets or users.

**Mitigation**:
- Non-custodial design: team cannot freeze user funds (only pause trading — user still owns + can refund/redeem)
- Legal counsel in multiple jurisdictions
- Geographic block at FE layer (if required by law) — but protocol remains accessible via CLI / direct contract calls
- Documentation clearly explains user responsibility for local law compliance

#### 3.5.2 Doxxed team targeted

**Threat**: Team members identified + pressured / legally threatened.

**Mitigation**:
- Multisig decentralization reduces single-point pressure
- External advisors on multisig (not team-only)
- Legal entity structure (DAO LLC or similar)
- Team identity policy: partial doxx for trust, plus pseudonymous signers to reduce individual risk

## 4. Defense layers summary

```
┌─────────────────────────────────────────────────────────┐
│ Layer 5: Community defense                              │
│  Bug bounty, public audit, transparency dashboards      │
└─────────────────────────────────────────────────────────┘
                         ↑
┌─────────────────────────────────────────────────────────┐
│ Layer 4: Observability                                  │
│  Metrics, alerts, on-chain audit trail, incident log    │
└─────────────────────────────────────────────────────────┘
                         ↑
┌─────────────────────────────────────────────────────────┐
│ Layer 3: Operational security                           │
│  Multisig 3/5, HSM, 48h timelock, role separation      │
│  Geographic distribution, key rotation, incident drill  │
└─────────────────────────────────────────────────────────┘
                         ↑
┌─────────────────────────────────────────────────────────┐
│ Layer 2: Smart contract implementation                  │
│  Reentrancy, invariants (7), custom errors, fuzz tests  │
│  Role-based access, storage isolation, fail-loud        │
└─────────────────────────────────────────────────────────┘
                         ↑
┌─────────────────────────────────────────────────────────┐
│ Layer 1: Protocol design                                │
│  Non-custodial, ERC-20 tokens, transparent upgrades     │
│  Stateless router, zero-custody, pause asymmetry        │
└─────────────────────────────────────────────────────────┘
```

## 5. Key management summary

Full detail: [Multisig & Key Management Policy](06-multisig-key-management.md).

**Key category summary:**

| Key | Storage | Rotation | Redundancy |
|---|---|---|---|
| Multisig signer keys (5x) | Hardware wallet per signer | Annual | 3/5 threshold |
| Deployer keys | Hardware wallet, locked safe | Per-deploy only | Single-use, rotated after |
| Indexer / BE service keys | HSM / cloud KMS | Quarterly | Regenerable |
| SIWE session secrets | Generated at runtime, not stored | Per-session | None needed |
| Fee recipient address | Multisig-controlled | Rotate rarely (causes UX disruption) | Multisig-backed |
| Oracle ManualOracle admin | Multisig (shared with main admin) | Same as admin rotation | 3/5 threshold |
| API keys (partner tier) | Generated, rotatable by partner | Per-partner request | Independent |

## 6. Monitoring & detection

### 6.1 Real-time detection

Automated alerts trigger investigation:

| Event | Detection | Response |
|---|---|---|
| Invariant violation (INV-1 to INV-7) | Periodic on-chain check + event-driven verifier | P0 incident, immediate pause |
| Router non-zero balance post-call | `Router_NonZeroBalance` revert event | P0 incident, investigate caller tx |
| Unusual volume spike (10x normal) | Prometheus anomaly | P1 alert, monitor |
| Oracle approval change | `OracleApproved` / `OracleRevoked` event | Verify was authorized |
| DiamondCut scheduled | `CallScheduled` event on Timelock | Public announcement, prep for execution |
| Pause event | `ModulePaused` event | Verify authorized, post public status |
| Multiple cancelled orders same user (>50/min) | Exchange event rate | P2 alert, may be bot misbehave |
| Session token anomaly (many simultaneous from different IPs) | BE log analysis | Revoke session, notify user |
| RPC outage (primary unreachable) | Health check | Auto-fallback, alert ops |

### 6.2 Weekly review

- Volume + trade count trends vs. expectations
- Admin activity report (all role changes, pauses, oracle approvals)
- Dispute summary
- Infrastructure incidents
- Bug bounty submissions triage

### 6.3 Monthly security review

- Red team exercise findings
- New threat intel (crypto industry news)
- Multisig signer attestation (all signers confirm key integrity)
- Access control audit (who has what)
- Backup + recovery drill verification

## 7. Incident classification & response

Cross-reference: [Incident Playbook](../security/05-incident-playbook.md).

| Severity | Example | Response time | Action authority |
|---|---|---|---|
| P0 | Active drain exploit | < 15 min | Pauser alone pauses + escalates |
| P0 | INV violation detected | < 15 min | Pause + all-hands investigation |
| P1 | Oracle reports wrong data (single market) | < 1h | Revoke oracle + refund mode |
| P1 | Compromised signer suspected | < 1h | Revoke role + rotate key |
| P2 | Infrastructure degraded | < 4h | Ops response + comms |
| P3 | UX bug, delayed indexing | < 24h | Normal workflow |

Full playbook: [Incident Playbook](../security/05-incident-playbook.md).

## 8. Third-party dependencies risk

### 8.1 Smart contract dependencies

| Dep | Version | Risk | Mitigation |
|---|---|---|---|
| OpenZeppelin Contracts | Pinned exact version | Historical excellence, but not immune | Audit our usage, not OZ |
| Uniswap v4-core | Pinned | Under audit by UNI team | We depend on v4 audit outcomes |
| Uniswap v4-periphery | Pinned | Under audit | Same |
| Permit2 | Canonical deployed | Live + battle-tested | Address hardcoded immutable |
| Chainlink AggregatorV3Interface | Interface only | Minimal | No risk from interface itself |

### 8.2 Off-chain dependencies

| Dep | Version | Risk | Mitigation |
|---|---|---|---|
| Node.js runtime | 24 LTS | Medium — runtime CVEs | Quarterly patch cycle |
| viem | 2.47 (pinned) | Low — active maintainer | Monitor releases |
| wagmi + RainbowKit | pinned | Low | Monitor releases |
| zod | 4.x | Low | Schema runtime validation |
| NestJS | 11.x | Low | Major version pinned |
| Mongoose | 9.x | Medium — ORM layer | Sanitize all queries, use typed schemas |
| Ponder | 0.16.6 | Medium — core indexer | Version pinned, careful with upgrades |
| PostgreSQL | 16 | Low | Standard DB |
| MongoDB Atlas | Managed | Low — vendor-managed | Trust vendor SOC 2 |

### 8.3 Infrastructure

| Provider | Service | Risk | Mitigation |
|---|---|---|---|
| Cloudflare | CDN + WAF + DNS | Vendor lock-in, potential data access | Nothing privacy-sensitive transits Cloudflare |
| MongoDB Atlas | Database | Same | Encryption at rest + in transit, TLS only |
| Unichain RPC (official) | Chain access | Single source | 2 fallback RPCs |
| Alchemy / QuickNode | Backup RPC | Vendor dependency | 2+ providers |
| Vercel | Frontend hosting | Vendor lock-in | Alternative: IPFS mirror |

## 9. Compliance considerations

### 9.1 Non-custodial posture

PrediX is **non-custodial** by design:

- User funds are in user-owned wallets OR in Smart Contract (not team accounts)
- Team cannot freeze user addresses
- Team cannot seize funds arbitrarily
- `refund` and `cancel` paths always accessible (not pause-gated)
- Admin can only pause "new trades" (MARKET module), never touch "exit paths"

This materially reduces regulatory risk in most jurisdictions (custody services typically have stricter regulations).

### 9.2 Privacy

On-chain data is **public by design**:
- Trades, positions, volume — all observable
- Address linkage — visible to anyone
- FE does not add additional PII (no email required for basic use)

Users seeking privacy should use privacy-preserving wallets / mixers (subject to local law). Team cannot enforce privacy beyond not collecting off-chain data.

### 9.3 KYC / AML

Phase 1: No KYC. Permissionless access.

Phase 2+: Optional KYC for:
- Higher trade limits
- Market creator verification
- Regulated jurisdiction compliance (if legally required)

Implementation: partner with KYC providers (Sum & Substance, Veriff). User retains non-KYC option but with limits.

## 10. Security testing

### 10.1 Automated

- CI runs full test suite per PR (743 tests, ~8min)
- Invariant fuzz (10K runs per CI) on critical INV-1 to INV-7
- Slither static analysis per PR
- Echidna fuzz on critical contracts (weekly in staging)
- Mythril / Manticore symbolic execution on changed functions

### 10.2 Manual

- Peer review — minimum 2 reviewers per PR, 1 for non-critical changes
- Security lead review on all changes to:
  - Access control
  - Reentrancy guards
  - External call patterns
  - Upgrade paths
- Quarterly security review (team exercise on current design)

### 10.3 External

- External audit — engaged, pending delivery
- Bug bounty — post-mainnet launch
- Red team exercise — pre-mainnet + annually thereafter
- Community open review — code public, encourage review

## 11. Disclosure & communication

### 11.1 Security advisory policy

When a vulnerability is fixed:

1. **T+0 (private)**: Patch deployed (testnet first if possible)
2. **T+7 days**: Reporter + team post-mortem
3. **T+30 days (default)**: Public disclosure advisory
4. Users alerted via Discord + Twitter + email list

Disclosure delay can extend for:
- Zero-day still being actively exploited
- Coordinated disclosure with other protocols

### 11.2 Transparency commitments

Published quarterly:

- Resolved vulnerabilities (severity + description)
- Infrastructure incidents (if any)
- Admin actions (pauses, oracle changes, upgrades)
- Multisig signer attestation results
- Red team exercise results (anonymized)

## 12. Open questions for auditors

Areas where we expect auditor input / challenge:

1. **Trusted router whitelist management**: Who maintains the list? Current: admin multisig. Is this acceptable trust model?
2. **Hook upgrade timelock (48h)**: Is this enough window for LP to exit if bad upgrade proposed?
3. **Emergency resolve 7d delay**: Too fast (pressure to bad decision) or too slow (user stuck)?
4. **Grace period 365d**: Industry-appropriate?
5. **Cache TTL 2s/60s**: Does hot 2s expose MEV surface for informed trader vs. less-informed?
6. **SIWE session 24h**: Reasonable vs. 1h (tighter) or 7d (better UX)?
7. **Pause asymmetry (exit always open)**: Any edge case where this allows attacker to drain pre-pause?

## 13. Roadmap

### Phase 1 (current — testnet)
- 7 invariants tested
- Internal audit complete
- External audit engaged

### Phase 2 (mainnet Q2 2026)
- External audit report published
- Bug bounty launched (Sherlock or Immunefi)
- Multisig 3/5 with geographic distribution
- UMA oracle dispute mechanism
- Formal incident drill program

### Phase 3 (scale, Q3-Q4 2026)
- Multi-feed oracle consensus
- Cross-chain security considerations
- Formal verification (Certora / Halmos) on critical invariants
- Expanded bug bounty ($250K+ cap)
- Quarterly external red team exercises

### Phase 4 (2027+)
- Decentralized oracle committee
- On-chain governance security
- Insurance fund for oracle error compensation
- Full protocol security budget (% of revenue)

## 14. References

- [System Architecture](00-system-architecture.md)
- [Smart Contract Spec](01-smart-contract-spec.md)
- [Oracle Design](02-oracle-design.md)
- [Multisig Policy](06-multisig-key-management.md)
- [Invariants](../security/02-invariants.md)
- [Incident Playbook](../security/05-incident-playbook.md)
- [Audit Reports](../security/01-audits.md)
- [Bug Bounty](../security/03-bug-bounty.md)

External references:
- [SWC Registry](https://swcregistry.io)
- [Consensys Smart Contract Best Practices](https://consensys.github.io/smart-contract-best-practices/)
- [Trail of Bits Building Secure Contracts](https://github.com/crytic/building-secure-contracts)
- [SCSVS](https://github.com/ComposableSecurity/SCSVS)
- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)

## 15. Changelog

- **v1.0** — 2026-04: Initial security design doc for Phase 1.
