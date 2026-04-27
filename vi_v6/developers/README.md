# Developers

Tích hợp PrediX vào app, bot, data pipeline.

## Đường tích hợp

```mermaid
flowchart LR
    Goal{Mục tiêu?}
    Goal -->|App user trade PrediX| R[Tích hợp Router contract]
    Goal -->|Fetch data on-chain| I[Indexer API]
    Goal -->|Dùng BE cache + metadata| BE[Backend API]
    Goal -->|Bot trade automated| BO[Bots & API key]
    Goal -->|Mobile / native app| M[Mobile / Wagmi]
    Goal -->|Listen events realtime| EV[Events reference]

    classDef opt fill:#dbeafe,stroke:#2563eb,color:#0f172a
    class R,I,BE,BO,M,EV opt
```

| Tôi muốn… | Đi tới |
|---|---|
| Cho user app trade PrediX markets | [Tích hợp Router](router-integration.md) |
| Fetch market list / portfolio / history | [Indexer API](indexer-api.md) |
| Dùng BE cache + metadata | [Backend API](backend-api.md) |
| Listen events realtime | [Events reference](events-reference.md) |
| Build trading bot | [Bots & API key](bots-api.md) |
| Tích hợp mobile / native | [Mobile / Wagmi](mobile-integration.md) |
| Test integrate trước mainnet | [Testnet info](testnet.md) |

## Quickstart — first call

```typescript
import { createPublicClient, http } from 'viem';
import { unichainSepolia } from 'viem/chains';  // testnet hiện tại
// import { unichain } from 'viem/chains';      // mainnet sau launch

const client = createPublicClient({
  chain: unichainSepolia,
  transport: http('https://sepolia.unichain.org'),
});

// Fetch market list từ testnet Indexer (gated — xem Testnet info để có endpoint)
const TESTNET_INDEXER = 'https://indexer.testnet.predix.app';  // example, real URL via Discord
const res = await fetch(`${TESTNET_INDEXER}/api/markets?limit=10`);
const { data: markets } = await res.json();

console.log(markets.map(m => `${m.question} — YES @ ${m.yesPrice}`));
```

Chi tiết integration step-by-step: [Tích hợp Router](router-integration.md). Get testnet endpoint: [Testnet info](testnet.md).

## Stack overview

| Layer | Stack | Language |
|---|---|---|
| Smart contracts | Foundry + Solidity 0.8.30 | Solidity |
| Indexer | Ponder + PostgreSQL + Hono | TypeScript |
| Backend | NestJS + Fastify + MongoDB + zod | TypeScript |
| Frontend | Next.js + React + Tailwind + viem + wagmi | TypeScript |
| Paymaster | ERC-4337 v0.7 + Pimlico bundler | Solidity + TS off-chain signer |

## API environments

| Env | Indexer API | Backend API |
|---|---|---|
| **Testnet** (live) | Gated access — xem [Testnet info](testnet.md) | Gated access — xem [Testnet info](testnet.md) |
| **Mainnet** (TBA) | `https://indexer.predix.app` | `https://api.predix.app` |

> Testnet endpoint hiện gated qua Discord #testnet-access (chống abuse). Mainnet endpoint sẽ public khi launch.

## Chain info

| | Testnet (live now) | Mainnet (TBA) |
|---|---|---|
| **Network** | Unichain Sepolia | Unichain |
| **Chain ID** | `1301` | `130` |
| **RPC public** | `https://sepolia.unichain.org` | `https://mainnet.unichain.org` |
| **Explorer** | [sepolia.uniscan.xyz](https://sepolia.uniscan.xyz) | [uniscan.xyz](https://uniscan.xyz) |
| **Status** | ✅ Beta live | 🟡 TBA — sau audit |

## Rate limits

| Tier | Public | Authenticated | Notes |
|---|---|---|---|
| Free | 60 req/min/IP | 300 req/min/user | Default |
| Pro | 600 req/min | 3000 req/min | $20/month, API key |
| Enterprise | Custom | Custom | Contact |

WebSocket: 10 conn/IP, unlimited messages.

## SDK (TBA)

Official SDK roadmap:

```bash
npm install @predix/sdk        # TypeScript / JavaScript
pip install predix-sdk         # Python
cargo add predix-sdk           # Rust
```

Pre-launch: dùng REST API + viem trực tiếp.

## Support

- **Bug bảo mật contract**: [security@predix.app](mailto:security@predix.app), bug bounty active.
- **API question**: Discord #dev-support.
- **Feature request**: GitHub issue [predix-protocol](https://github.com/predix-protocol).
- **Enterprise**: [business@predix.app](mailto:business@predix.app).

## Licensing

| Component | License | Note |
|---|---|---|
| Smart contracts | BUSL-1.1 → GPLv3 sau 2 năm | Business Source License, non-commercial |
| Indexer | MIT | Open source |
| Backend | MIT | Open source |
| Frontend | MIT | Open source |
| SDK | MIT | Open source |
