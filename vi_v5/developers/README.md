# Developers

Tích hợp PrediX vào app, bot, hoặc data pipeline của bạn.

## 3 đường tích hợp

Chọn theo use case:

| Tôi muốn… | Dùng gì | Đi tới |
|---|---|---|
| Cho user app của tôi trade PrediX markets | Router contract | [Tích hợp Router](router-integration.md) |
| Fetch market list / portfolio / history | Indexer API | [Indexer API](indexer-api.md) |
| Dùng BE cache + metadata của PrediX | Backend API | [Backend API](backend-api.md) |
| Build bot off-chain listen events | Events reference | [Events](events-reference.md) |

## Quickstart

### Clone repo

```bash
git clone https://github.com/predix-protocol/Final_Predix_V2
cd Final_Predix_V2
```

### Local setup

```bash
# Smart contracts
cd SC
forge install
forge build
forge test

# Indexer
cd ../INDEXER
pnpm install
pnpm codegen
pnpm test

# Backend
cd ../BE
pnpm install
pnpm test

# Frontend
cd ../FE
pnpm install
pnpm dev
```

Env file cần config: copy `.env.example` → `.testenv.local` mỗi subtree, điền addresses của testnet (xem [addresses.md](../giao-thuc/addresses.md)).

## Ngôn ngữ / stack theo layer

| Layer | Stack | Language |
|---|---|---|
| SC | Foundry + Solidity 0.8.30 | Solidity |
| Indexer | Ponder 0.16 + PostgreSQL + Hono | TypeScript |
| BE | NestJS 11 + Fastify + MongoDB + zod | TypeScript |
| FE | Next.js 16 + React 19 + Tailwind 4 + viem + wagmi | TypeScript |
| Paymaster | ERC-4337 v0.7 | Solidity + TypeScript off-chain signer |

## API environments

| Env | Indexer API | Backend API |
|---|---|---|
| Testnet | (internal cloudflared tunnel) | `https://cells-depends-prize-raising.trycloudflare.com` |
| Staging | TBD | TBD |
| Mainnet | (not deployed) | (not deployed) |

## Chain info

- Testnet: Unichain Sepolia (1301) — RPC `https://sepolia.unichain.org`
- Mainnet: Unichain (130) — RPC `https://mainnet.unichain.org` (chưa deploy)

## Rate limit

- Public endpoint: 60 req/min/IP.
- Authenticated (SIWE session): 300 req/min.
- WebSocket: 10 conn/IP.

## Support

- Bug bảo mật contract: [security@predix.app](mailto:security@predix.app)
- API questions: Discord (xem [Liên kết](../tai-nguyen/links.md))
- Feature request: GitHub issue.

## Licensing

- Smart contracts: `BUSL-1.1` (non-commercial, Business Source License) — 2 năm, sau chuyển `GPLv3`.
- BE / Indexer / FE: `MIT`.
- SDK (sau TGE): `MIT`.
