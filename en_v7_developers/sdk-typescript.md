# TypeScript SDK

> **Status: Coming soon.** The official TypeScript SDK is under development. Use the REST API + viem directly in the meantime.

## Planned

```bash
npm install @predix/sdk
```

```typescript
import { PrediXClient } from '@predix/sdk';

const client = new PrediXClient({
  chain: 'testnet',
  rpcUrl: 'https://sepolia.unichain.org',
});

// Markets
const markets = await client.markets.list({ status: 'active' });
const market = await client.markets.get(1);

// Trading
const quote = await client.trade.quote('buyYes', 1, '100');
const tx = await client.trade.buyYes(1, '100', { slippage: 50 });

// Portfolio
const positions = await client.portfolio.get('0x...');

// WebSocket
client.ws.subscribe('orderbook', { marketId: 1 });
client.ws.on('data', (msg) => console.log(msg));
```

## Current alternative

Use [viem](https://viem.sh) directly with Router contract:

→ [Quickstart — TypeScript](quickstart-typescript.md)

→ [Router integration](router-integration.md)

→ [API reference](api-reference.md) — REST endpoints via `fetch`
