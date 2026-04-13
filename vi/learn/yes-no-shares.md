---
description: Hiểu về token YES và NO
---

# Giải thích cổ phần YES và NO

## Chúng là gì?

Khi giao dịch trên PrediX, bạn đang mua **token** (cổ phần kỹ thuật số):

- **Token YES** = "Tôi nghĩ điều này sẽ xảy ra"
- **Token NO** = "Tôi nghĩ điều này sẽ không xảy ra"

## Chúng có giá trị bao nhiêu?

**Trước khi market kết thúc** (đang giao dịch):
- YES và NO có giá thị trường từ $0.01 đến $0.99
- Giá thay đổi dựa trên hoạt động mua/bán của trader

**Sau khi market kết thúc** (giải quyết):
- Token **thắng** = **$1.00** (có thể đổi lấy USDC)
- Token **thua** = **$0.00** (vô giá trị)

## Ví dụ

```
Market: "Chelsea có thắng Man City không?"

Trước trận:
  YES (Chelsea thắng) = $0.35
  NO (Chelsea không thắng) = $0.65

Sau trận — Chelsea thắng 2-1:
  YES = $1.00 ← Người thắng!
  NO = $0.00

Sau trận — Man City thắng 3-0:
  YES = $0.00
  NO = $1.00 ← Người thắng!
```

## Bạn có thể làm gì với cổ phần?

| Hành động | Cách | Khi nào |
|--------|-----|------|
| **Mua** | Trả USDC → nhận YES hoặc NO | Bất cứ lúc nào trước khi market kết thúc |
| **Bán** | Bán YES hoặc NO → nhận USDC | Bất cứ lúc nào trước khi market kết thúc |
| **Giữ** | Chờ giải quyết | Cho đến khi market được giải quyết |
| **Nhận thưởng** | Cổ phần thắng → $1 USDC mỗi cổ phần | Sau khi giải quyết |

## Nên mua bao nhiêu?

Số cổ phần = số tiền tiềm năng bạn nhận được.

```
Mua 50 YES ở $0.40 → Chi phí: $20
Nếu YES thắng → Nhận: $50 → Lợi nhuận: $30
Nếu NO thắng → Nhận: $0 → Lỗ: $20
```

> 💡 **Mẹo**: Chỉ đầu tư số tiền bạn chấp nhận mất được. Bắt đầu nhỏ ($25-$50) trong khi học.

## Market đa kết quả

Một số market có nhiều hơn 2 kết quả:

```
"Ai sẽ thắng bầu cử tổng thống Mỹ 2028?"
  → Token YES/NO của Trump
  → Token YES/NO của DeSantis
  → Token YES/NO của Newsom
  → Token YES/NO của Harris
  → Token YES/NO của Other

Chỉ MỘT kết quả có thể thắng. Mua YES cho người bạn nghĩ sẽ thắng.
```

---

**Tiếp theo**: [Cách kiếm tiền](making-money.md) · [Giao dịch đầu tiên](../user-guide/getting-started/first-trade.md)
