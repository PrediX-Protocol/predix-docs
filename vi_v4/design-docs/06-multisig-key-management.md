---
description: Multisig configuration (3/5 Phase 1, 4/7 Phase 2), timelock, role separation, key management policy, signer responsibilities.
---

# Multisig & Key Management Policy

**Document status**: v1.0 — 2026-04 (Phase 1 config); Phase 2 scheduled Q3 2026
**Authority**: This policy is mandatory for all multisig signers. Deviations require written sign-off from Security Lead + Operations Lead + minimum 2 signers.
**Audience**: Multisig signers (internal + external), audit firms reviewing key management, regulatory reviewers

---

## 1. Philosophy

**Principle**: No single human controls user funds. Every high-impact action requires multi-party agreement + time for community review.

**Design constraints:**

1. **Threshold cryptography** — N-of-M multisig, not single key
2. **Time delay** — 48h timelock on upgrades (non-emergency)
3. **Separation of powers** — DEFAULT_ADMIN ≠ CUT_EXECUTOR ≠ OPERATOR ≠ PAUSER
4. **Hardware wallets** — all keys in HSM-grade hardware, never software-only
5. **Geographic distribution** — signers across 3+ continents to reduce coordinated physical attack risk
6. **Transparent processes** — all admin actions on-chain + publicly observable

## 2. Multisig schema

### 2.1 Phase 1 — 3/5 Gnosis Safe (mainnet launch, Q2 2026)

```
┌──────────────────────────────────┐
│ PrediX Multisig Admin Safe       │
│ Signers: 5                       │
│ Threshold: 3                     │
│ Storage: Gnosis Safe on Unichain │
└──────────────────────────────────┘
       │
       ├── DEFAULT_ADMIN_ROLE (grants/revokes other roles)
       ├── ADMIN_ROLE (protocol config: fees, caps, oracle approval)
       └── ManualOracle admin (resolution signing)

┌──────────────────────────────────┐
│ PrediX Operator Safe             │
│ Signers: 3                       │
│ Threshold: 2                     │
└──────────────────────────────────┘
       │
       └── OPERATOR_ROLE (emergencyResolve, enableRefundMode)

┌──────────────────────────────────┐
│ PrediX Pauser Safe               │
│ Signers: 3                       │
│ Threshold: 2                     │
└──────────────────────────────────┘
       │
       └── PAUSER_ROLE (pause/unpause module)

┌──────────────────────────────────┐
│ TimelockController contract      │
│ (only holds CUT_EXECUTOR_ROLE)   │
│ delay: 48h                       │
│ proposers: [Admin Safe]          │
│ executors: [address(0)] (open)   │
└──────────────────────────────────┘
       │
       └── CUT_EXECUTOR_ROLE (only this, not admin — NEW-01)
```

### 2.2 Signer composition (5 signers, Admin Safe)

Identity not public (phishing risk reduction). Role composition:

| Seat | Role | Expertise |
|---|---|---|
| 1 | Security Lead | Security engineering, incident response |
| 2 | CTO / Engineering Lead | Protocol architecture, smart contracts |
| 3 | Operations Lead | Day-to-day ops, deployment, monitoring |
| 4 | External Technical Advisor | Independent review — not team employee, industry reputation |
| 5 | External Trust / Community | DAO representative, community-elected OR vetted advisor |

Rationale:
- **3 internal + 2 external**: prevents pure-team unilateral action
- **External seats rotate**: 18-month terms (announce rotation at year 1 to community)
- **Technical balance**: not all seats are operators — some are reviewers

### 2.3 Phase 2 — 4/7 upgrade (Q3 2026, post-launch)

Upgrade rationale: after protocol traction, higher threshold gives stronger security guarantee while still allowing operational velocity.

New composition (2 additional seats):

| Seat | Role |
|---|---|
| 1-5 | (from Phase 1) |
| 6 | Validator Representative | From top LP or staking partner |
| 7 | Institutional Partner | From major user / integrator (conflict disclosed) |

Threshold: **4/7** — requires broader agreement.

### 2.4 Phase 3 (2027+) — Decentralized governance

Target: 9/15 community-elected signers with on-chain governance. Details in future governance doc.

## 3. Signer responsibilities

### 3.1 Individual commitments

Each signer agrees to:

1. **Key security** — hardware wallet only, no software signing, no screen-share during ceremonies
2. **Availability** — respond to multisig signing requests within 24h (emergency: 2h)
3. **Due diligence** — review every tx content before signing, NEVER rubber-stamp
4. **Transparency** — disclose conflicts of interest to other signers (e.g., holding significant position in market being resolved)
5. **Annual attestation** — written confirmation of key integrity + signing practices
6. **Security hygiene** — no shared devices, regular OS patches, strong authentication on email/messenger
7. **Discretion** — do NOT publicly reveal signing status to reduce phishing
8. **Emergency protocol** — follow incident response SOP if compromise suspected

### 3.2 Collective responsibilities

Signers collectively:

- Review + approve admin actions (fee changes, oracle approvals, cap adjustments)
- Sign upgrades via TimelockController
- Assess refund mode / emergency resolve decisions (via multi-channel discussion)
- Annual security review of policy + procedures
- Rotate seats + keys per schedule

### 3.3 Signer compensation

- Internal team signers: part of role responsibility (no additional pay for signing)
- External advisor signers: compensated via:
  - Annual retainer (target: $20K USD/year, adjustable)
  - Per-action signing fee ($500 / action, capped)
  - OR equity / token grants post-tokenization

Compensation documented in signer agreement. Conflicts disclosed.

## 4. Key management

### 4.1 Key types

| Key category | Storage | Backup | Rotation |
|---|---|---|---|
| Multisig signer keys | Hardware wallet (Ledger Nano X or S Plus) | Seed phrase in bank safe deposit box + trusted family member (dual custody) | Annual or on compromise |
| Deployer keys (for contract deployment) | Hardware wallet, locked + offline | Seed phrase offline cold storage | Per-deploy, rotate after each major deploy |
| Service keys (BE, Indexer) | Cloud KMS (AWS KMS, GCP KMS) | Cloud provider backup | Quarterly |
| Emergency recovery key | Bank vault, dual custody | Paper backup cold storage | On rotation of seats |

### 4.2 Hardware wallet config

**Mandatory:**
- Ledger Nano X (current) or Nano S Plus (alternative)
- PIN 8 digits minimum
- Passphrase (25th word) for advanced seed protection
- Firmware updated within 30 days of official release
- Apps limited to: Ethereum, viewing only those needed

**Forbidden:**
- Trezor not approved (minor cryptographic implementation differences not certified for our policy; may approve in future after audit)
- Any software wallet (MetaMask browser extension, mobile wallets)
- Shared device for signing
- Keys on cloud storage (Google Drive, Dropbox, iCloud)

### 4.3 Seed phrase management

Per-signer policy:

1. **Generation**: generate in hardware wallet (never computer)
2. **Recording**: write on metal backup (Cryptosteel, Zeus, Keystone) + store in fireproof safe
3. **Backup copy**: second metal backup in different physical location (bank safe deposit box)
4. **Dual custody**: trusted family member holds backup location + decryption passphrase for recovery
5. **Never**: digital storage, photos, text messages, email, cloud backups

**Recovery scenario**: signer incapacitated → trusted family member retrieves backup → new signer onboarded → role transferred via multisig vote.

### 4.4 Key rotation schedule

Annual rotation (recommended):

1. **T-30 days**: Schedule rotation, coordinate signers' availability
2. **T-0**: Generate new seed on new hardware wallet
3. **T+1 day**: Add new signer address to multisig (via existing 3/5 signing)
4. **T+3 days**: Remove old signer address (3/5 signing to remove)
5. **T+7 days**: Verify old key no longer has any role; old hardware wallet wiped
6. **T+30 days**: Old seed metal backup destroyed (cut in half, pieces in separate locations)

Emergency rotation (compromise suspected):

1. **Immediate**: Pause affected module
2. **T+1h**: Admin Safe votes to revoke compromised signer
3. **T+4h**: New signer onboarded (or interim reduced threshold)
4. **T+24h**: Public post-mortem
5. **T+7d**: Full key rotation for other signers (precaution)

### 4.5 Safe deposit box details

- Signer's personal bank safe deposit box (each signer uses different bank)
- Box contains:
  - Metal seed backup (1 piece, split from secondary location)
  - Hardware wallet passphrase (on paper, separate from seed)
  - Access instructions for trusted family member
- Annual verification: signer physically accesses + confirms intact
- Insurance: individual signer responsibility; can opt into policy if desired

## 5. Role separation enforcement

### 5.1 `DEFAULT_ADMIN_ROLE`

- **Holder**: Admin Safe (3/5)
- **Authority**: Grant/revoke all other roles
- **Constraint**: Cannot call `diamondCut` directly. Must propose via TimelockController.
- **Last-admin protection**: Cannot revoke the last `DEFAULT_ADMIN_ROLE` member (contract-enforced: `AC_LastAdminProtection()`)

### 5.2 `ADMIN_ROLE`

- **Holder**: Admin Safe (3/5)
- **Authority**: Protocol config (fees, caps, oracle approvals, market creation)
- **Constraint**: No upgrade authority. No pause authority. No user fund access (non-custodial design).

### 5.3 `OPERATOR_ROLE`

- **Holder**: Operator Safe (2/3)
- **Authority**: Emergency resolve (after 7d delay), enable refund mode
- **Constraint**: Cannot change protocol config. Cannot upgrade. Cannot grant roles. 7-day delay enforces caution.

### 5.4 `PAUSER_ROLE`

- **Holder**: Pauser Safe (2/3)
- **Authority**: Pause/unpause modules
- **Constraint**: Pause is recoverable (not irreversible). Pause DOES NOT disable exit paths (refund, cancel, sweep).

### 5.5 `CUT_EXECUTOR_ROLE`

- **Holder**: **TimelockController contract ONLY**
- **Authority**: Execute `diamondCut` after 48h delay
- **Constraint**: This is the upgrade choke point. Deployment procedure:
  1. Deploy TimelockController with 48h delay
  2. Grant `CUT_EXECUTOR_ROLE` to TimelockController
  3. Revoke `CUT_EXECUTOR_ROLE` from deployer + Admin Safe
  4. Verify: `getRoleMemberCount(CUT_EXECUTOR_ROLE) == 1 && getRoleMember(0) == timelockAddress`
- **NEW-01 audit finding**: Even DEFAULT_ADMIN cannot regrant CUT_EXECUTOR to bypass timelock.

### 5.6 Role authority matrix

| Action | DEFAULT_ADMIN | ADMIN | OPERATOR | PAUSER | CUT_EXECUTOR | TimelockController |
|---|---|---|---|---|---|---|
| `grantRole` | ✅ | ❌ | ❌ | ❌ | ❌ | N/A |
| `revokeRole` | ✅ | ❌ | ❌ | ❌ | ❌ | N/A |
| `createMarket`, `createEvent` | ❌ | ✅ | ❌ | ❌ | ❌ | N/A |
| `setApprovedOracle` | ❌ | ✅ | ❌ | ❌ | ❌ | N/A |
| `setDefaultRedemptionFeeBps` | ❌ | ✅ | ❌ | ❌ | ❌ | N/A |
| `pause(module)` | ❌ | ❌ | ❌ | ✅ | ❌ | N/A |
| `unpause(module)` | ❌ | ❌ | ❌ | ✅ | ❌ | N/A |
| `emergencyResolve` (+7d delay) | ❌ | ❌ | ✅ | ❌ | ❌ | N/A |
| `enableRefundMode` | ❌ | ❌ | ✅ | ❌ | ❌ | N/A |
| `sweepUnclaimed` (+365d) | ❌ | ✅ | ❌ | ❌ | ❌ | N/A |
| `diamondCut` | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ (via CUT_EXECUTOR) |
| `schedule` upgrade call | N/A | Proposer | N/A | N/A | N/A | ✅ |
| `execute` scheduled call | N/A | N/A | N/A | N/A | N/A | Anyone (after delay) |

## 6. Signing ceremony procedures

### 6.1 Standard admin operation (non-upgrade)

Example: `setDefaultRedemptionFeeBps(500)` (reduce fee)

1. **Proposal**: Operations Lead drafts tx via Safe UI
2. **Discussion**: Private Slack/Signal channel, minimum 24h review
3. **Signing** (within 48h window):
   - Signer A: Opens Safe UI on computer, verifies tx content on hardware wallet screen (exact values: function, args)
   - Signer A signs via hardware wallet → pushed to Safe queue
   - Signers B + C: same process independently
4. **Execution**: Once 3 signatures collected, any signer can execute on-chain
5. **Confirmation**: Transaction hash posted in Discord `#governance`

### 6.2 Upgrade operation (diamondCut)

1. **Design review**: Security + CTO + external advisor review upgrade
2. **Test on staging**: Deploy + test on Unichain Sepolia for 2 weeks
3. **Public announcement**: Blog post + Discord explaining change (T-14 days)
4. **Schedule via TimelockController**:
   - Admin Safe multisig vote 3/5 to `schedule(target=Diamond, data=diamondCut(...), delay=48h)`
   - Emits `CallScheduled` — publicly observable
5. **48h delay window**:
   - Community reviews + discusses
   - If concern: Admin Safe can `cancel(id)` with 3/5 vote
   - Public monitoring: watchers alert if suspicious
6. **Execute after delay**: Any address can call `execute(id)` (open executor)
7. **Verify**: Diamond state updated as expected, run smoke test
8. **Announce completion**: Discord + Twitter

### 6.3 Signing hardware wallet verification

**CRITICAL**: Signer must verify tx contents on hardware wallet screen.

Hardware wallet shows (simplified):
```
Contract: 0xF38a265E6e4F57D000a1CC08004da5B4A380B08A
Function: setDefaultRedemptionFeeBps
Argument: 500 (5%)
```

**If hardware wallet shows different address/function/value than expected**:
- DO NOT SIGN
- Cancel signing session
- Alert other signers + Security Lead
- Investigate: compromised Safe UI? Compromised browser? Malware?

### 6.4 Emergency signing

For time-critical actions (pause, revoke compromised oracle):

- **Threshold flex**: Pauser Safe and Operator Safe allow 2/3 (faster than 3/5 Admin)
- **Async signing**: Signers can sign from different time zones — coordination via encrypted messaging
- **Signing within 2h**: On-call signers expected to respond within 2h for P0 events
- **Post-facto review**: Always a formal post-mortem within 7d

## 7. Audit trail

### 7.1 On-chain audit trail

All admin actions emit events:

- `RoleGranted`, `RoleRevoked`, `RoleAdminChanged`
- `ModulePaused`, `ModuleUnpaused`
- `OracleApproved`, `OracleRevoked`
- `DiamondCut`
- `UpgradeProposed`, `Upgraded`, `UpgradeCancelled` (Hook proxy)
- `CallScheduled`, `CallExecuted`, `Cancelled` (TimelockController)

These events are **immutable** + indexed by Ponder. Public queries:

- `GET /api/v2/admin/role-changes`
- `GET /api/v2/admin/pauses`
- `GET /api/v2/admin/diamond-cuts`
- `GET /api/v2/admin/fee-changes`
- `GET /api/v2/admin/hook-proxy-upgrades`

### 7.2 Off-chain audit trail

Internal:
- Slack/Signal conversation archive (encrypted)
- Multisig UI tx history
- Sign ceremony session logs (per signer's own record)

External (published):
- Monthly admin activity report on status.predixpro.io
- Quarterly governance dashboard: action types, signer attendance, dispute summary

## 8. Phishing & social engineering defense

### 8.1 Known attack patterns

| Attack | Example | Defense |
|---|---|---|
| Fake Safe UI | Malicious browser extension showing modified tx data | Always verify on hardware wallet screen |
| Impersonated signer on messenger | "Hi, it's me, please sign this URGENT" | Multi-channel verification (voice call mandatory for urgent) |
| Contract verification misdirection | Request from forged GitHub issue to sign malicious bytes | Only sign from approved signing sessions |
| Domain spoofing | `predixpro.io` vs. `predixppro.io` lookalike | Bookmark canonical URL, never paste from Discord DMs |
| Social engineering via expired NFT / airdrop | "Click to sign to claim" | Multisig signers never sign arbitrary non-PrediX contracts from their signer wallet |

### 8.2 Standard practices

- Multisig signers NEVER sign any non-PrediX transaction from their signer key
- Multisig signers NEVER share signing session via screen-share
- Multisig signers NEVER give API key / private key / seed / passphrase via any channel
- Multisig signers NEVER respond to urgent requests without multi-channel confirmation (Slack + voice call)
- Multisig signers NEVER use unknown hardware wallets or devices

### 8.3 Annual phishing drill

Quarter 1 (January) each year:

- Security Lead designs drill scenario (inspired by real-world phishing)
- Unannounced (no advance notice to signers)
- Fake request sent to 1-2 signers
- Post-drill review: did they detect? What would have caught it?
- Improvements to SOP based on findings

## 9. Compromise response

### 9.1 Suspected signer key compromise

**Signer self-reports** OR detected via anomalous activity:

1. **Immediate (< 30 min)**:
   - Signer notifies Security Lead + Operations Lead (encrypted channel)
   - Pauser Safe votes 2/3 to `pause(MARKET)` + `pause(DIAMOND)` (precautionary)
2. **Short-term (< 2h)**:
   - Admin Safe 3/5 vote to revoke compromised address from roles:
     ```
     multisig.revokeRole(DEFAULT_ADMIN_ROLE, compromisedAddress)
     multisig.revokeRole(ADMIN_ROLE, compromisedAddress)  // if held
     multisig.revokeRole(OPERATOR_ROLE, compromisedAddress)  // if held
     // ManualOracle.setAdmin(newAdmin) if needed
     ```
3. **Same day (< 24h)**:
   - Replace signer with new key
   - Forensic analysis of compromise (how did it happen?)
4. **Week 1**:
   - Public post-mortem
   - Review all actions signed by compromised key within suspected compromise window
   - If any bad actions: attempt reversal via compensation mechanism (treasury payout if on-chain irreversible)
5. **Month 1**:
   - Full rotation of ALL signers' keys (precaution — compromise method may affect others)

### 9.2 Lost signer key

If signer loses key (hardware failure, lost device, etc.):

1. Signer uses seed backup to restore on new hardware wallet (same address preserved)
2. If seed also lost: signer declared "offline"
3. Remaining signers vote to remove offline signer + add replacement
4. No immediate security risk (key not in attacker's hands)

### 9.3 Signer coerced / held against will

Worst case: signer forced to sign malicious tx.

**Defenses:**

- Duress codeword: pre-agreed phrase in ceremony language ("I want coffee"); if signer uses in communication, other signers pause + investigate
- Threshold provides some protection: attacker must coerce 3/5 simultaneously (geographic distribution helps)
- Response: freeze funds via pause, revoke coerced signer, rotate keys
- Legal backstop: signers operate under legal agreement; coerced action is void if proven

## 10. Signer onboarding

### 10.1 New signer checklist

Before being added to Safe:

- [ ] Background check + references (external advisors)
- [ ] Legal agreement signed (confidentiality, responsibilities, compensation)
- [ ] Hardware wallet acquired + verified (Ledger serial check)
- [ ] Passphrase generated + stored per policy
- [ ] Metal seed backup in bank safe deposit box
- [ ] Trusted family member briefed on recovery procedure
- [ ] Training session completed (1h call): signing ceremony, phishing awareness, incident response
- [ ] Phishing drill passed
- [ ] Emergency contact info submitted (primary + backup phone / email)
- [ ] Added to encrypted team chat (Signal group)
- [ ] First sign ceremony observed (non-decision signature to test setup)

### 10.2 Address registration

New signer address added to Safe:

1. Existing threshold votes to call `addOwnerWithThreshold(newAddr, newThreshold)` on Safe contract
2. Wait confirmation block
3. New signer verifies they can see tx queue
4. Role grants pending — DEFAULT_ADMIN votes to grant required roles to Safe (Safe address, not individual signer)

## 11. Policy review & update

### 11.1 Review cadence

- **Quarterly**: Security Lead + Operations Lead review effectiveness
- **Annually**: Full policy review with all signers
- **Ad-hoc**: After any incident or industry-wide attack pattern change

### 11.2 Change procedure

1. Proposal drafted by Security Lead
2. Minimum 2 weeks open comment (internal signers + external advisors)
3. Approval: 4/5 of Admin Safe (supermajority for policy)
4. Updated policy published (this doc)
5. Training updates delivered
6. Communication to community (material changes)

### 11.3 Version history tracking

All policy versions retained. Changelog at bottom of this doc. Governance decisions on policy changes documented in team wiki + published summary to community.

## 12. Regulatory compliance

### 12.1 Non-custodial nature

Multisig controls:
- Role grants on smart contracts
- Protocol configuration
- Emergency pause

Multisig does NOT control:
- User wallets / private keys
- User funds (held in smart contract, user-withdrawable via `refund` / `redeem`)
- User trading decisions

This architecture supports non-custodial regulatory classification in most jurisdictions.

### 12.2 Disclosure obligations

Per jurisdiction:
- Entity registration (per DAO LLC structure in Delaware / Wyoming / similar)
- Tax implications for signer compensation
- Sanctions compliance: signer addresses checked against OFAC-style lists
- KYB for institutional partnerships (handled at partnership level, not signer level)

## 13. Redundancy & continuity

### 13.1 Business continuity plan

If team disbands / key personnel unavailable:

- **3/5 threshold** allows 2 signer gap
- Remaining 3 signers can continue operations
- Emergency contact in legal agreement allows successor to access backup
- Open-source codebase ensures protocol continues even without team

### 13.2 Protocol continuity without team

- Smart contracts: self-sufficient post-deploy
- Indexer: self-hostable from public RPC
- Backend: optional (UI can talk to Indexer directly in degraded mode)
- Frontend: IPFS mirror accessible

**Worst case**: Team disappears. Users can:
- Continue redeeming resolved markets (no team action needed)
- Trade via Router on-chain (no BE needed)
- Run own Indexer + BE if desired
- Refund any market (if in refund mode) or wait for resolution (oracle-dependent)

Team disappearance does NOT mean fund loss — that's the non-custodial guarantee.

## 14. Appendices

### A. Current multisig addresses (testnet)

| Safe | Address |
|---|---|
| Admin Safe | TBA (testnet admin = single EOA currently; mainnet = Gnosis Safe) |
| Operator Safe | TBA |
| Pauser Safe | TBA |
| TimelockController | TBA |

**Mainnet addresses**: Published at mainnet deploy (Q2 2026).

### B. Tools

- **Gnosis Safe**: [safe.global](https://safe.global)
- **Hardware wallets**: Ledger Nano X / S Plus
- **Encryption**: Signal (signer communication), GPG (email attachments)
- **Storage**: Cryptosteel / Zeus (metal seed backup), bank safe deposit box

### C. Legal / governance references

- Signer agreement template (internal)
- DAO LLC formation docs (internal)
- OFAC sanctions check procedure (internal)
- External advisor NDA template (internal)

### D. Related docs

- [Security Design](04-security-design.md)
- [System Architecture](00-system-architecture.md)
- [Access Control & Timelock](../smart-contracts/33-access-control-safety.md)
- [Incident Playbook](../security/05-incident-playbook.md)
- [Timelock & Upgrade Governance](../security/04-timelock-governance.md)

## 15. Changelog

- **v1.0** — 2026-04: Initial policy for Phase 1 mainnet (3/5 admin).
- Future:
  - **v1.1** — Phase 2 upgrade (4/7 admin)
  - **v2.0** — Phase 3 community-elected signers

## 16. Signer acknowledgment

Each multisig signer must read + acknowledge this policy by signing an off-chain attestation (stored in internal records). Annual re-acknowledgment required.

**Acknowledgment format** (paraphrased):

> I, [signer], acknowledge I have read and understood the PrediX Multisig & Key Management Policy v1.0 as of [date]. I commit to the practices and procedures specified, including key security, signing procedures, phishing awareness, incident response, and regular reviews. I understand my responsibilities as a signer and the implications of multisig threshold cryptography for PrediX user funds.
>
> I will notify [Security Lead] immediately of any suspected compromise, loss, or deviation from this policy.

Signed: [signer signature, on paper with notarization for external seats, internal attestation for team]
