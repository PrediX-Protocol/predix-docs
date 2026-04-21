---
description: Cách lấy ABIs, generate TypeScript types, và setup typed clients (viem + openapi-fetch).
---

# ABIs & Typed Clients

## Lấy ABIs

Có 3 cách, chọn 1:

### 1. Download trực tiếp từ GitHub

```bash
BASE=https://raw.githubusercontent.com/predix-protocol/indexer/main/abis

curl -O $BASE/MarketFacetAbi.json
curl -O $BASE/EventFacetAbi.json
curl -O $BASE/AccessControlFacetAbi.json
curl -O $BASE/PausableFacetAbi.json
curl -O $BASE/DiamondCutFacetAbi.json
curl -O $BASE/PrediXRouterAbi.json
curl -O $BASE/PrediXExchangeAbi.json
curl -O $BASE/PrediXHookV2Abi.json
curl -O $BASE/PrediXHookProxyV2Abi.json
curl -O $BASE/ManualOracleAbi.json
curl -O $BASE/ChainlinkOracleAbi.json
curl -O $BASE/OutcomeTokenAbi.json
curl -O $BASE/PoolManagerAbi.json
```

### 2. Generate từ source (Foundry)

```bash
git clone https://github.com/predix-protocol/contracts
cd contracts/packages/diamond
forge build
cat out/MarketFacet.sol/MarketFacet.json | jq '.abi' > MarketFacet.abi.json
```

### 3. Etherscan API

```bash
curl "https://api.sepolia.uniscan.xyz/api?module=contract&action=getabi&address=0xF38a265E6e4F57D000a1CC08004da5B4A380B08A&apikey=YOUR_KEY"
```

Diamond ABI trên explorer là proxy; để lấy full facet ABIs phải query từng facet contract riêng.

---

## Typed contract client với viem

viem `getContract` hỗ trợ typed calls:

```typescript
import { getContract } from 'viem';
import type { Abi } from 'viem';

import MarketFacetAbi from './abis/MarketFacetAbi.json' with { type: 'json' };

const diamond = getContract({
  address: '0xF38a265E6e4F57D000a1CC08004da5B4A380B08A',
  abi: MarketFacetAbi as Abi,
  client: { public: publicClient, wallet: walletClient },
});

// TypeScript autocomplete tất cả function
const market = await diamond.read.getMarket([42n]);
await diamond.write.splitPosition([42n, 10_000_000n]);

// Listen event
diamond.watchEvent.MarketResolved({
  onLogs: logs => console.log('Resolved:', logs),
});
```

### As-const import (viem best practice)

viem compute types từ ABI literal. Export ABI as const:

```typescript
// abis/MarketFacetAbi.ts
export const MarketFacetAbi = [
  {
    type: 'function',
    name: 'getMarket',
    inputs: [{ type: 'uint256', name: 'marketId' }],
    outputs: [{
      type: 'tuple',
      components: [
        { type: 'string', name: 'question' },
        { type: 'uint256', name: 'endTime' },
        // ...
      ],
    }],
    stateMutability: 'view',
  },
  // ... other functions
] as const;

// Use
import { MarketFacetAbi } from './abis/MarketFacetAbi';

const market = await publicClient.readContract({
  address: DIAMOND,
  abi: MarketFacetAbi,          // ← as const
  functionName: 'getMarket',
  args: [42n],
});
// market.question autocompletes, typed
```

Tool: [abitype](https://github.com/wevm/abitype) có `parseAbi` giúp convert JSON ABI sang as-const TypeScript.

---

## TypeChain (cho ethers v6 user)

Nếu team dùng ethers v6:

```bash
pnpm add --save-dev typechain @typechain/ethers-v6
pnpm typechain --target ethers-v6 --out-dir src/typechain-types 'abis/*.json'
```

Import:

```typescript
import { MarketFacet__factory } from './typechain-types';

const diamond = MarketFacet__factory.connect(DIAMOND_ADDRESS, signer);
const market = await diamond.getMarket(42n);
```

---

## Backend API typed client (openapi-fetch)

### Install

```bash
pnpm add openapi-fetch
pnpm add -D openapi-typescript
```

### Generate types

```bash
npx openapi-typescript https://api-testnet.predixpro.io/openapi/v2.json \
  -o src/types/api-v2.d.ts
```

Output: `api-v2.d.ts` với type `paths` + `components`.

### Create client

```typescript
import createClient from 'openapi-fetch';
import type { paths } from './types/api-v2.d.ts';

export const apiClient = createClient<paths>({
  baseUrl: 'https://api-testnet.predixpro.io',
  // optional: auth interceptor
  fetch: async (input, init) => {
    const token = localStorage.getItem('predix:sessionToken');
    const headers = new Headers(init?.headers);
    if (token) headers.set('Authorization', `Bearer ${token}`);
    return fetch(input, { ...init, headers });
  },
});

// Fully typed usage
const { data, error } = await apiClient.GET('/api/v2/markets/{id}', {
  params: { path: { id: '0xabc...' } },
});

if (data) {
  console.log(data.data.title.fallback);  // TS autocomplete
}
```

### Unwrap envelope helper

BE response wraps `{ data, meta }`. Unwrap utility:

```typescript
export function unwrap<T>(res: { data: { data: T; meta: any } | undefined; error: any }): T {
  if (res.error) throw new PrediXError(res.error.error.code, res.error.error.message);
  if (!res.data) throw new Error('No data');
  return res.data.data;
}

const market = unwrap(await apiClient.GET('/api/v2/markets/{id}', { params: { path: { id } } }));
```

---

## Zod schema snapshot (optional, FE pattern)

FE có thể snapshot zod schemas từ BE để có runtime validation + shared types. Script:

```bash
# In FE project
pnpm add zod
# Copy zod schemas từ BE repo
cp -r BE/src/schemas/v2/* ./src/lib/api/schemas/
```

Verify sync với hash check:

```bash
pnpm check:schemas-sync
```

Pattern này cho phép FE `safeParse` BE response, catch drift early.

---

## SDK roadmap

Hiện tại PrediX chưa có official SDK package (`@predix/sdk`) — integrator tự build client với viem + openapi-fetch pattern trên. Official SDK dự kiến Q3 2026:

- `@predix/sdk-contracts` — typed contract wrappers + ABIs bundled
- `@predix/sdk-api` — typed REST client + helpers
- `@predix/sdk-react` — React hooks (useMarket, usePortfolio, useTrade)

Subscribe [Changelog](../tai-nguyen/changelog.md) để biết khi release.
