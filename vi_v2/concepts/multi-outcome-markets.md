# Thị trường nhiều kết quả

Category nhóm các market nhị phân liên quan nhau cho các sự kiện có nhiều hơn hai kết quả.

## Cơ chế

**Ví dụ:** "Ai sẽ thắng bầu cử Tổng thống Mỹ 2028?"

Thay vì tạo một market, ta tạo một **category** với một market nhị phân cho mỗi ứng viên:

| Market | Câu hỏi |
|--------|----------|
| Market A | "Ứng viên A có thắng không?" |
| Market B | "Ứng viên B có thắng không?" |
| Market C | "Ứng viên C có thắng không?" |

Mỗi market có token YES/NO riêng. Category đảm bảo đúng một kết quả thắng.

## Thao tác nhóm

### groupSplit

Nạp USDC một lần để nhận token YES cho **mọi** market trong category.

```
$100 USDC → 100 YES_A + 100 YES_B + 100 YES_C + ... (mỗi market một token)
```

### groupMerge

Trả về một bộ đầy đủ token YES (mỗi market một token) để nhận USDC.

```
100 YES_A + 100 YES_B + 100 YES_C → $100 USDC
```

## Phân xử

```typescript
// ADMIN_ROLE phân xử cả category
// winningOutcomeIndex = chỉ số của market thắng (0-based)
await diamond.resolveCategory(categoryId, 0); // Ứng viên A thắng
```

## Tiếp theo

- [Split & Merge](split-and-merge.md) — split/merge cho market đơn
- [Phân xử](resolution.md) — cơ chế resolution
- [Developer: Tạo Market](../developers/create-market.md) — cách tạo category
