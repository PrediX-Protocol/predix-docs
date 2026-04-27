# Giao thức

Kiến trúc kỹ thuật của PrediX. Cho advanced user, researcher, developer muốn hiểu sâu.

## 4 lớp

```mermaid
flowchart TB
    User([👤 User])
    FE["<b>FE</b> — Next.js + React<br/>App router · Adapter layer · AA wallet"]
    BE["<b>BE</b> — NestJS + Fastify + MongoDB<br/>Modules · Schema-first zod · Cache 2-tier"]
    IDX["<b>Indexer</b> — Ponder + PostgreSQL<br/>Contract subscriptions · Tables · Hono REST API"]
    SC["<b>Smart Contracts</b> — Solidity 0.8.30<br/>Diamond · Hook · Exchange · Router · Oracle · Paymaster<br/>Unichain Sepolia (testnet live) · Unichain mainnet (TBA)"]

    User -->|HTTPS| FE
    FE -->|REST /api/v2| BE
    BE -->|REST read-only| IDX
    IDX -->|RPC + events| SC
    User -.->|tx ký từ ví| SC

    classDef ground fill:#1e293b,stroke:#0f172a,color:#fff
    classDef layer fill:#52525b,stroke:#3f3f46,color:#fff,stroke-width:1.5px
    class SC ground
    class FE,BE,IDX layer
```

Data flow **một chiều**: SC emit events → Indexer sync → BE serialize → FE render. BE không ghi ngược Indexer/SC. User ký tx trực tiếp với SC qua ví — không qua BE.

## Nguyên tắc thiết kế

1. **Non-custodial** — user luôn giữ private key. Protocol không giữ tiền trung gian.
2. **Router stateless** — `balanceOf(router) == 0` enforce on-chain sau mỗi call.
3. **Composable ERC-20** — outcome token là ERC-20 standard, plug được vào DeFi stack.
4. **Separation of concerns** — layer nào việc nấy, không cross-boundary import.
5. **Fail-loud** — không silent fallback. Bất biến sai → revert / throw.
6. **Upgrade qua timelock 48h** — không emergency bypass.

## Đọc theo thứ tự

1. [Architecture & contracts](architecture.md) — packages, Diamond + facets, Hook, Exchange, Router, Paymaster, deployed addresses (testnet + mainnet)
2. [Oracle](oracle.md) — Manual, Chainlink, UMA, Committee
3. [Bảo mật & timelock](bao-mat.md) — Invariants, audit posture, 48h delay, incident response

Muốn integrate code → [Developers](../developers/README.md).
