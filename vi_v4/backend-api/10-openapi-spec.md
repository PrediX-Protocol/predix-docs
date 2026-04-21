---
description: OpenAPI v2 spec — auto-generated từ zod schemas. Download + embed trong docs.
---

# OpenAPI Spec

PrediX BE expose **OpenAPI 3.1** spec auto-generated từ zod schemas. Không có hand-written OpenAPI — nếu code đổi, regenerate + commit trong cùng PR.

## Download

### Production

```
GET https://api-testnet.predixpro.io/openapi/v2.json
```

### Source (repo)

```
BE/openapi/v2.json
```

File này tracked in git — view latest: [github.com/predix-protocol/backend/blob/main/openapi/v2.json](https://github.com/predix-protocol).

## Sử dụng OpenAPI spec

### 1. Generate TypeScript types (FE / bot)

Dùng `openapi-typescript` (FE) hoặc tương tự:

```bash
npx openapi-typescript https://api-testnet.predixpro.io/openapi/v2.json \
  -o src/types/api-v2.d.ts
```

Hoặc từ file local:

```bash
npx openapi-typescript ./BE/openapi/v2.json -o ./src/types/api-v2.d.ts
```

### 2. Generate typed client với `openapi-fetch`

```typescript
import createClient from 'openapi-fetch';
import type { paths } from './types/api-v2.d.ts';

const client = createClient<paths>({
  baseUrl: 'https://api-testnet.predixpro.io',
});

// Fully typed
const { data, error } = await client.GET('/api/v2/markets', {
  params: {
    query: {
      kind: 'binary',  // ← TypeScript autocomplete
      status: 'active',
      limit: 50,
    },
  },
});

if (data) {
  // data.data is typed as Market[]
  console.log(data.data[0].title.fallback);
}
```

### 3. Import vào Postman / Insomnia / Bruno

- Postman: File → Import → chọn `openapi/v2.json` → auto-generate collection
- Insomnia: Create → Import from → URL → `https://api-testnet.predixpro.io/openapi/v2.json`
- Bruno: File → Open Collection → import OpenAPI

### 4. Render trong GitBook

GitBook hỗ trợ OpenAPI block. Admin có thể embed:

````
```openapi
schemaUrl: https://api-testnet.predixpro.io/openapi/v2.json
operationId: listMarkets
```
````

→ render endpoint UI với "Try it" playground.

## Spec structure

```yaml
openapi: 3.1.0
info:
  title: PrediX BE_V2 API
  version: 2.0.0
  description: Prediction market exchange — backend v2
paths:
  /api/v2/markets:
    get:
      operationId: listMarkets
      tags: [Markets]
      ...
components:
  schemas:
    Market:
      ...
    MoneyAmount:
      ...
```

## Regenerate (maintainers)

```bash
cd BE
pnpm gen:openapi      # read zod → write openapi/v2.json
pnpm check:contract   # verify committed === regenerated (CI gate)
```

Drift = reject PR.

## Sync với FE types

FE có 2 pattern sync:

1. **Direct OpenAPI**: `openapi-fetch` consume spec trực tiếp — types auto từ spec.
2. **Zod schema sync**: BE cũng export zod schemas dưới dạng snapshot files. FE import zod cho runtime validation + infer TS types. Xem [ABIs & Typed Clients](../developers/39-abi-types.md).

## Endpoint count snapshot (2026-04-21)

~60 endpoints total:

| Group | Count |
|---|---|
| Markets | 15 |
| Events | 5 |
| Users & Portfolio | 12 |
| Auth | 5 |
| Rewards & Gamification | 6 |
| Social | 7 |
| Notifications | 4 |
| Admin | 15 |
| Health | 2 |
| Protocol & Stats | 2 |

## Changelog

- **2.0.0** (2026-04): Initial v2 release với SIWE, zod-first schemas, 2-tier caching
- **2.0.x**: Patch releases theo từng phase triển khai (SPEC §20)

Versioning: **semver**. Breaking change → bump major + sunset cũ theo policy 90 ngày.
