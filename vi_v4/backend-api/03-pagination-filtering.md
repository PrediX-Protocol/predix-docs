---
description: Pagination (limit/offset), filtering (query params), sorting — convention chung cho list endpoints.
---

# Pagination, Filtering, Sorting

## Pagination — offset-based

Mọi list endpoint (`/markets`, `/events`, `/trades`, `/orders`, `/holders`…) hỗ trợ:

| Query param | Kiểu | Default | Max | Mô tả |
|---|---|---|---|---|
| `limit` | integer | 20 | 100 | Số item mỗi page |
| `offset` | integer | 0 | unlimited | Số item skip |

```
GET /api/v2/markets?limit=50&offset=100
```

Response có `meta.pagination`:

```json
{
  "data": [...],
  "meta": {
    "timestamp": 1776090000,
    "version": "v2",
    "pagination": {
      "limit": 50,
      "offset": 100,
      "total": 1234
    }
  }
}
```

{% hint style="warning" %}
Với list lớn (>10K), offset cao có thể chậm. Khi có cursor-based pagination (Q3 2026 roadmap) — FE sẽ nhận `meta.pagination.cursor` thay cho offset.
{% endhint %}

## Filtering — query parameters

Filter được document per-endpoint, type-safe qua zod.

### Ví dụ: `/api/v2/markets`

| Param | Kiểu | Values |
|---|---|---|
| `kind` | enum | `binary` \| `scalar` \| `multi` \| `sports` \| `grouped` |
| `status` | enum | `active` \| `resolved` \| `refunding` |
| `eventId` | string | ID của event (bigint dec string) |
| `isFeatured` | enum | `"true"` \| `"false"` |
| `tag` | string | Tag do admin gán |
| `categoryId` | string | Category ID |
| `q` | string | Full-text search on title (1-200 chars) |
| `includeEventChildren` | boolean | `true` → include child markets của events |

```
GET /api/v2/markets?kind=binary&status=active&tag=crypto&sortBy=volume&limit=10
```

### Ví dụ: `/api/v2/users/:address/orders`

```
GET /api/v2/users/0xabc.../orders?status=open&marketId=0x123...&limit=50
```

Không truyền `status` → trả về **tất cả status** (open + filled + cancelled). Không có default filter ẩn (Bug #1 prevention).

## Sorting

| Param | Kiểu | Values |
|---|---|---|
| `sortBy` | enum | Per-endpoint; phổ biến: `newest`, `volume`, `ending_soon`, `featured`, `trending` |
| `sortOrder` | enum | `asc` \| `desc` (default tùy endpoint) |

### Ví dụ sort modes cho `/markets`

| `sortBy` | Ý nghĩa |
|---|---|
| `newest` | Sort theo `createdAt` desc |
| `volume` | Sort theo `lifetime volume` desc |
| `ending_soon` | Sort theo `endTime` asc, filter implicit `status=active` |
| `featured` | Admin-featured markets first |
| `trending` | Alias của `volume` + auto-apply `status=active` nếu client không pass status explicit |

## Boolean values

Boolean được truyền dưới dạng **string literal** `"true"` hoặc `"false"` (không phải 1/0).

## Date/time query

Dùng **unix seconds integer** cho `since`, `until`:

```
GET /api/v2/trades?since=1776000000&until=1776090000
```

Không dùng ISO strings trong query.

## Null / empty

- Missing query param = không filter
- `?q=` (empty string) → invalid request, 400 (zod validate `minLength: 1`)

## Example errors

Truyền `kind=invalid`:

```json
{
  "error": {
    "code": "invalid_request",
    "message": "Validation failed",
    "details": [
      {
        "path": "kind",
        "message": "Expected one of [binary, scalar, multi, sports, grouped]"
      }
    ]
  },
  "meta": { ... }
}
```

Truyền `limit=500`:

```json
{
  "error": {
    "code": "invalid_request",
    "message": "limit must be ≤ 100",
    "details": [{ "path": "limit", "message": "Expected ≤ 100" }]
  }
}
```

## No default filter — hard rule

{% hint style="warning" %}
List endpoint **không được** default filter ẩn data. Nếu user không pass `status` → trả về tất cả status. Đây là Bug #1 prevention từ SPEC.
{% endhint %}

Các default filter **documented** (allowed):

| Endpoint | Default filter | Lý do |
|---|---|---|
| `/markets` (no flag) | Exclude event children (markets có `eventId != 0`) | Tránh UI flood với child markets; canonical access qua `/events/:id` |
| `/markets?sortBy=trending` | Auto-apply `status=active` | "Trending" semantically chỉ apply cho active markets |
| `/markets?sortBy=ending_soon` | Auto-apply `status=active` | "Ending soon" không có nghĩa với resolved |

Mọi default filter khác = bug → report.
