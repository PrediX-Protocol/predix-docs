# Giao thức

Kiến trúc kỹ thuật của PrediX. Dành cho advanced user, researcher, developer muốn hiểu sâu.

## 4 lớp

```mermaid
flowchart TB
    User([👤 Người dùng])
    FE["<b>FE</b> — Next.js 16 + React 19<br/>App router · Adapter layer · AA wallet"]
    BE["<b>BE</b> — NestJS 11 + Fastify + MongoDB<br/>30 modules · Schema-first zod · Cache 2-tier"]
    IDX["<b>Indexer</b> — Ponder 0.16 + PostgreSQL<br/>13 contract subscriptions · 28 tables"]
    SC["<b>Smart Contracts</b> — Solidity 0.8.30<br/>Diamond · Hook · Exchange · Router · Oracle<br/>Unichain Sepolia 1301 (mainnet Q2 2026)"]

    User -->|HTTPS| FE
    FE -->|REST /api/v2| BE
    BE -->|REST read-only| IDX
    IDX -->|RPC + events| SC
    User -.->|tx ký từ ví| SC

    classDef ground fill:#1e293b,stroke:#0f172a,color:#fff
    classDef layer fill:#f1f5f9,stroke:#64748b,color:#0f172a
    class SC ground
    class FE,BE,IDX layer
```

Data flow **một chiều**: SC emit events → Indexer sync → BE serialize → FE render. BE không ghi ngược Indexer/SC. User ký tx trực tiếp với SC qua ví — không qua BE.

## Nguyên tắc thiết kế

1. **Non-custodial** — user luôn giữ private key. Protocol không giữ tiền trung gian.
2. **Router stateless** — `balanceOf(router) == 0` sau mỗi call, enforce on-chain.
3. **Composable ERC-20** — outcome token là ERC-20, plug được vào DeFi stack.
4. **Separation of concerns** — layer nào việc nấy, không cross-boundary import.
5. **Fail-loud** — không silent fallback. Bất biến sai → revert / throw.

## Đọc theo thứ tự

1. [Smart contracts](smart-contracts.md) — 6 package, Diamond + facets, Hook, Exchange, Router
2. [Oracle](oracle.md) — Manual / Chainlink / UMA / Committee phases
3. [Bảo mật & timelock](bao-mat.md) — Invariants, audit posture, 48h delay
4. [Contract addresses](addresses.md) — Testnet + mainnet

Muốn tích hợp code → [Developers](../developers/README.md).
