---
description: Cách Router tổng hợp thanh khoản CLOB và AMM
---

# Định tuyến thông minh

PrediXRouter tự động tìm đường thực hiện tốt nhất trên cả CLOB và AMM.

## Cách hoạt động

```
router.buyYes($100):
  Bước 1: Kiểm tra CLOB cho lệnh SELL_YES có giá tốt hơn AMM
  Bước 2: Khớp lệnh CLOB trước (giá tốt nhất, không trượt giá)
  Bước 3: Định tuyến số lượng còn lại qua Uniswap v4 AMM
  Bước 4: Người dùng nhận tổng token YES từ cả hai nguồn
```

### Ví dụ

```
Mua 100 token YES:
  CLOB: 60 YES có sẵn @ $0.62 → chi phí: $37.20
  AMM:  40 YES @ trung bình ~$0.65 → chi phí: $26.00
  Tổng: 100 YES với giá $63.20

Không có Smart Router (chỉ AMM):
  100 YES @ trung bình ~$0.66 → chi phí: $66.00
  
  Tiết kiệm: $2.80 (thực hiện tốt hơn 4.2%)
```

## Các hàm truy vấn giá

Luôn truy vấn giá trước khi giao dịch:

```typescript
const router = new ethers.Contract(ROUTER_ADDRESS, ROUTER_ABI, provider);

const yesOut = await router.quoteBuyYes(marketId, usdcIn);
const usdcOut = await router.quoteSellYes(marketId, yesIn);
const noOut = await router.quoteBuyNo(marketId, usdcIn);
const usdcFromNo = await router.quoteSellNo(marketId, noIn);
```

## Khi nào CLOB tốt hơn AMM

| Điều kiện | Sàn tốt hơn |
| --------- | ------------ |
| Sổ lệnh sâu, chênh lệch giá hẹp | CLOB |
| Không có lệnh tại giá mong muốn | AMM |
| Lệnh lớn | Chia đều cả hai |
| Gần hết hạn thị trường (phí AMM cao) | CLOB |

---

**Tiếp theo**: [Công cụ khớp lệnh](matching-engine.md) · [Định giá NO ảo](virtual-no-pricing.md)
