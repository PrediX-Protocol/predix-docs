---
description: Security overview — threat model, trust boundaries, và roadmap audit/bounty.
---

# Security Overview

{% hint style="warning" %}
**PrediX đang ở testnet**. Mainnet launch pending external audit chính thức. Docs security này describe current state + roadmap — không phải attestation cho production-readiness mainnet.
{% endhint %}

## Principles

1. **Fail-loud**: Mọi boundary validate + revert với typed error. Không silent default.
2. **Defense in depth**: Multiple independent guards (reentrancy EIP-1153, role-based access, pause, timelock upgrade, identity commit).
3. **Minimize trust**: Router zero-custody; Hook validate identity commit; Diamond re-verify oracle at resolve.
4. **Separation of powers**: `ADMIN` ≠ `CUT_EXECUTOR` — ngay cả admin gốc không thể upgrade without 48h timelock.
5. **Transparent upgrade**: Mọi DiamondCut + Hook upgrade emit events được index + public audit trail.

## Threat model

### In-scope threats

| Threat | Mitigation |
|---|---|
| **Reentrancy** | `TransientReentrancyGuard` (EIP-1153) trên 20+ external functions |
| **Sandwich attack qua Router** | Identity commit EIP-1153 — Hook reject swap không có committed identity |
| **Oracle manipulation** | Oracle approval list; re-verify at resolve (FINAL-D-03); Chainlink staleness + L2 sequencer check |
| **Admin retroactively raise fee** | Redemption fee snapshot at `createMarket` (FINAL-H04) |
| **Admin bypass timelock upgrade** | `CUT_EXECUTOR_ROLE` = TimelockController only; `DEFAULT_ADMIN` cannot bypass (NEW-01) |
| **Stuck Router funds** | Post-call `balanceOf(router) == 0` assertion (INV-3) — tested in 83 test cases |
| **Invalid resolution** | Oracle resolution monotonic (INV-6) — cannot revert once set |
| **Integer overflow** | Solidity 0.8.30 built-in + explicit `unchecked` with comments |
| **External contract reverts** | SafeERC20 for all transfers; fallback paths for hook callbacks |

### Out-of-scope (user responsibility)

- Wallet / private key security
- Phishing sites imitating PrediX
- User giving away signature to attacker (SIWE unclear context)
- Network-level attacks (DDoS on public RPC)

## Trust boundaries

```
┌───────────────────────────────────────────────────────────┐
│ Untrusted: User's wallet, RPC provider, external oracles  │
└───────────────────────────────────────────────────────────┘
                         ↓ signatures, price data
┌───────────────────────────────────────────────────────────┐
│ Smart Contracts (trust root after audit)                  │
│                                                            │
│  Diamond ←→ Hook ←→ Exchange ←→ Router                    │
│     ↓                                                      │
│  Oracle (approved)                                        │
└───────────────────────────────────────────────────────────┘
                         ↓ events
┌───────────────────────────────────────────────────────────┐
│ Indexer (trust derived from chain via finalized tag)      │
└───────────────────────────────────────────────────────────┘
                         ↓ HTTP
┌───────────────────────────────────────────────────────────┐
│ Backend (trust derived from Indexer + Mongo overlays)     │
└───────────────────────────────────────────────────────────┘
                         ↓ REST
┌───────────────────────────────────────────────────────────┐
│ Frontend / Bot (trust BE envelope; verify on-chain for    │
│                 critical state)                            │
└───────────────────────────────────────────────────────────┘
```

## Security resources

| Resource | Link |
|---|---|
| Audit reports | [Audit Reports](01-audits.md) |
| Critical invariants (7) | [Invariants](02-invariants.md) |
| Bug bounty program | [Bug Bounty](03-bug-bounty.md) |
| Timelock + upgrade governance | [Timelock & Upgrade](04-timelock-governance.md) |
| Incident playbook | [Incident Playbook](05-incident-playbook.md) |
| Custom errors registry | [Custom Errors](../smart-contracts/custom-errors.md) |

## Known limitations

1. **Testnet only**: Mainnet requires external audit completion + incident response drill.
2. **No formal verification**: Invariants tested via fuzz + property tests, không có Certora/Halmos proof chính thức yet.
3. **Single chain**: Cross-chain resolution (UMA, Wormhole) chưa implement — Phase 2 roadmap.
4. **RPC monoculture**: Indexer dependent trên Unichain official RPC + 2 backups. Multi-RPC consensus chưa có.
5. **Admin multisig cấu hình**: Multisig threshold (3-of-5 hay 5-of-9?) sẽ được công bố trước mainnet.

## Compliance note

PrediX **không** giữ custody của user fund. Protocol là non-custodial — user control private key + signing. Team không thể freeze user address (except pause toàn bộ module MARKET — không affect refund/cancel/withdrawal).

Legal framework: xem [Bối Cảnh Pháp Lý](../tong-quan-du-an/boi-canh-phap-ly.md).

## Contact

- Non-emergency security questions: `security@predixpro.io`
- Responsible disclosure: xem [Bug Bounty](03-bug-bounty.md)
- Emergency (active exploit in progress): PGP-encrypted email `security@predixpro.io` + DM team lead on TG
