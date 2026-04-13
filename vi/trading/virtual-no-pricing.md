---
description: Cách token NO được định giá mà không cần pool AMM riêng
---

# Định giá NO ảo

Token NO không có pool AMM riêng. Router tổng hợp giao dịch NO bằng cách sử dụng pool YES/USDC và các thao tác tách/gộp.

## Mua NO

```
1. Tách USDC → YES + NO
2. Bán YES vào pool AMM
3. Giữ lại token NO

Người dùng trả USDC → nhận token NO
```

## Bán NO

```
1. Mua YES từ pool AMM
2. Gộp YES + NO → USDC
3. Trả USDC cho người dùng

Người dùng trả token NO → nhận USDC
```

## Mối quan hệ giá

Do neo giá tách/gộp:

```
Giá NO ≈ $1.00 - Giá YES

Nếu YES giao dịch ở $0.70:
  NO ≈ $0.30
```

Điều này được thực thi bằng kinh doanh chênh lệch giá — bất kỳ sai lệch nào cũng tạo cơ hội lợi nhuận đẩy giá trở lại.

## Từ góc nhìn người dùng

Router xử lý tất cả sự phức tạp bên trong. Người dùng chỉ cần gọi:

```typescript
// Mua NO — giống như mua YES
await router.buyNo(marketId, usdcIn, mintAmount, minNoOut, recipient, deadline);

// Bán NO — giống như bán YES
await router.sellNo(marketId, noIn, minUsdcOut, recipient, deadline);
```

> ⚠️ **Chi phí gas**: Giao dịch NO bao gồm các bước bổ sung (tách + swap hoặc swap + gộp), nên gas cao hơn khoảng 2.5 lần so với giao dịch YES. CLOB hỗ trợ giao dịch NO trực tiếp mà không có chi phí phụ này.

---

**Tiếp theo**: [Lệnh thị trường](market-orders.md) · [Định tuyến thông minh](smart-routing.md) · [Phí](../concepts/fees.md)
