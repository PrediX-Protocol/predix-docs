---
description: Full error code registry với HTTP status, meaning, và fix action.
---

# Error Codes

Error codes là **closed enum** — BE chỉ emit các code trong bảng dưới. FE/bot nên switch exhaustive trên `error.code` để xử lý UX phù hợp.

## Generic errors

| Code | HTTP | Ý nghĩa | Client action |
|---|---|---|---|
| `invalid_request` | 400 | Body/query fail zod validation | Hiển thị `details[]` cho user, fix input |
| `auth_required` | 401 | Thiếu hoặc invalid session token | Emit `predix:session-expired`, re-prompt SIWE |
| `forbidden` | 403 | Role/ownership không đủ | Hiển thị "Bạn không có quyền" |
| `not_found` | 404 | Resource không tồn tại (chung) | 404 page |
| `rate_limit_exceeded` | 429 | Quá giới hạn rate | Back off theo `Retry-After` header |
| `internal_error` | 500 | Unexpected server error — đã log + alert | "Thử lại sau", report với `X-Request-Id` |
| `indexer_unavailable` | 503 | Upstream Ponder down hoặc slow | Retry với exponential backoff |

## Domain-specific

| Code | HTTP | Ý nghĩa |
|---|---|---|
| `market_not_found` | 404 | `marketId` không tồn tại |
| `market_not_active` | 409 | Market ended/resolved/refunding — không trade được |
| `market_paused` | 423 | Module MARKET đang pause |
| `event_not_found` | 404 | `eventId` không tồn tại |
| `order_not_found` | 404 | `orderId` không tồn tại |
| `user_not_found` | 404 | Address chưa có activity |
| `invalid_address` | 400 | Address không valid hex/checksum |
| `invalid_market_id` | 400 | marketId không valid bytes32 |
| `siwe_nonce_expired` | 400 | Challenge nonce expired (>10 phút) |
| `siwe_nonce_consumed` | 400 | Nonce đã dùng |
| `siwe_signature_invalid` | 400 | Signature không match address |
| `siwe_chain_mismatch` | 400 | Chain ID trong message ≠ server config |

## Admin-specific

| Code | HTTP | Ý nghĩa |
|---|---|---|
| `admin_role_required` | 403 | Cần `admin` role |
| `operator_role_required` | 403 | Cần `operator` role |
| `market_display_conflict` | 409 | Display metadata drift với on-chain state |
| `event_markets_missing` | 400 | Event overlay reference marketIds chưa tồn tại on-chain |

## Full error response shape

```json
{
  "error": {
    "code": "invalid_request",
    "message": "Validation failed",
    "details": [
      { "path": "limit", "message": "Expected ≤ 100, got 500" },
      { "path": "kind", "message": "Invalid enum value" }
    ]
  },
  "meta": {
    "timestamp": 1776090000,
    "version": "v2"
  }
}
```

- `code`: enum từ list trên — safe để switch exhaustive
- `message`: human-readable (EN) — log + display raw nếu không i18n
- `details`: optional array — thường là zod validation issues

## Client helper pattern (TypeScript)

```typescript
class PrediXError extends Error {
  constructor(
    public code: string,
    message: string,
    public details?: Array<{ path: string; message: string }>,
  ) {
    super(message);
  }
}

async function call<T>(path: string): Promise<T> {
  const res = await fetch(path);
  if (!res.ok) {
    const body = await res.json();
    throw new PrediXError(body.error.code, body.error.message, body.error.details);
  }
  return (await res.json()).data;
}

try {
  await call('/api/v2/markets/invalid-id');
} catch (err) {
  if (err instanceof PrediXError) {
    switch (err.code) {
      case 'auth_required': return reAuth();
      case 'rate_limit_exceeded': return await backoff();
      case 'market_not_found': return show404();
      default: return showGeneric(err.message);
    }
  }
  throw err;
}
```

## i18n fallback

Khi FE muốn localize error messages:

- Map `error.code` → i18n key (vd `error.auth_required`)
- Nếu miss key → fallback `error.message` (EN)
- `details[].message` thường technical — không localize, chỉ dev xem

## Logging + debugging

Mọi response (success/error) có `X-Request-Id` header. Khi report bug:

1. Copy `X-Request-Id`
2. Paste vào bug report
3. Team lookup trace log — thấy full request/response + exception

Không bao giờ expose raw stack trace trong `error.message` cho production.
