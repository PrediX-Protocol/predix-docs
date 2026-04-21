---
description: Response envelope, error shape, primitives (address, price, time, LocalizedString).
---

# Response Envelope

Mọi response của BE v2 tuân theo **2 format thống nhất**: success envelope và error envelope.

## Success envelope

```json
{
  "data": <payload>,
  "meta": {
    "timestamp": 1776090000,
    "version": "v2"
  }
}
```

Với list endpoint có thêm pagination:

```json
{
  "data": [ ... ],
  "meta": {
    "timestamp": 1776090000,
    "version": "v2",
    "pagination": {
      "limit": 20,
      "offset": 0,
      "total": 145
    }
  }
}
```

## Error envelope

```json
{
  "error": {
    "code": "market_not_found",
    "message": "Market with id 0xabc... does not exist",
    "details": []
  },
  "meta": {
    "timestamp": 1776090000,
    "version": "v2"
  }
}
```

### Error code enum (closed set)

| Code | HTTP status | Use case |
|---|---|---|
| `auth_required` | 401 | Thiếu hoặc invalid session token |
| `forbidden` | 403 | Authenticated but role không đủ |
| `not_found` | 404 | Resource (market, user, order) không tồn tại |
| `market_not_found` | 404 | Specific — market id không tìm thấy |
| `invalid_request` | 400 | Body/query không pass zod validation |
| `rate_limit_exceeded` | 429 | Request quá fast |
| `indexer_unavailable` | 503 | Upstream Indexer không reachable |
| `internal_error` | 500 | Unexpected — log + alert |

Chi tiết: [Error Codes](09-error-codes.md).

## Primitives bất biến

BE enforce format nghiêm ngặt tại boundary. FE/bot **không cần** và **không được** transform thêm.

### Ethereum addresses

**Luôn lowercase**, 42 ký tự (`0x` + 40 hex).

```json
"trader": "0xfad5a0e43cf8cffd3f7f0bb56b74a85a41a7ca7d"
```

❌ Không: checksum `0xFaD5...`, mixed case, `0X...`.

### Market ID (bytes32)

Tuỳ indexer: **bigint decimal string** hoặc **66-char hex string** (`0x` + 64 hex). Indexer emit `uint256`, BE serialize thành hex:

```json
"marketId": "0xabc0000000000000000000000000000000000000000000000000000000000000"
```

### Price / probability

**Decimal string**, 0.0001 precision:

```json
{ "yesPrice": "0.524", "noPrice": "0.476" }
```

❌ Không dùng `0.524` JS number (floating-point loss). FE nên dùng `decimal.js` hoặc `bignumber.js` khi compute.

### Money amount

**Object** 4 field:

```json
{
  "collateral": {
    "decimal": "150.5",
    "raw": "150500000",
    "decimals": 6,
    "unit": "USDC"
  }
}
```

- `decimal`: human-readable string
- `raw`: big-integer string, chia `10^decimals` → decimal
- `unit`: "USDC" / "YES" / "NO" / …

### Timestamp

**Unix seconds integer**:

```json
"createdAt": 1776090000
```

❌ Không ISO string `"2026-04-21T10:00:00Z"`, không millis `1776090000000`. FE convert bằng `new Date(timestamp * 1000)`.

### User-visible strings (LocalizedString)

Strings hiển thị cho user được wrap để enable i18n:

```json
"title": {
  "key": "market.0xabc.title",
  "fallback": "Will BTC > $100K by Q3 2026?"
}
```

FE lookup `key` trong locale file; fallback nếu miss. Xem [FE i18n pattern](../developers/39-abi-types.md).

### Colors / icons (SemanticToken)

```json
"statusColor": {
  "tokenKey": "semantic.positive",
  "literal": null
}
```

FE map `tokenKey` sang CSS var / color system. `literal` chỉ dùng khi absolutely cần hex.

### Status + capabilities

Status là **enum string computed server-side**:

```json
{
  "status": "active",
  "capabilities": ["trade", "limit", "split", "merge"]
}
```

FE **không** tự compute status từ `endTime > now && !isResolved`. FE check `capabilities[]` để quyết định show button nào.

Possible capabilities:

- `trade` — user có thể market order
- `limit` — user có thể place limit order
- `cancel` — user có thể cancel order
- `split` / `merge`
- `redeem` — market resolved, có thể claim
- `refund` — market in refund mode

## Discriminated union cho polymorphic type

Market có nhiều kind: binary, multi, scalar, grouped, sports. BE dùng Stripe-style discriminator:

```json
{
  "kind": "binary",
  "binary": {
    "yesToken": "0x...",
    "noToken": "0x...",
    "yesPrice": "0.524",
    "noPrice": "0.476"
  }
}
```

Hoặc:

```json
{
  "kind": "multi",
  "multi": {
    "eventId": "42",
    "members": [ ... ]
  }
}
```

FE switch exhaustive: `market[market.kind]` — TS exhaustiveness check catch missing case.

## Envelope generation (for maintainers)

- Global `TransformInterceptor` wrap data thành `{ data, meta }`
- Global `HttpExceptionFilter` map typed errors thành `{ error, meta }`
- Controller **không** wrap thủ công
- Envelope format là hard rule — violation = reject PR

Xem `BE/src/common/interceptors/` và `BE/src/common/filters/`.
