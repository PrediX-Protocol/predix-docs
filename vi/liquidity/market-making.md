---
description: Tao lap thi truong CLOB — cung cap thanh khoan so lenh
---

# Tao lap Thi truong

## Tong quan

Tao lap thi truong tren CLOB bao gom viec dat dong thoi lenh mua va lenh ban de nam bat chenh lech gia.

## Chien luoc co ban

```typescript
const exchange = new ethers.Contract(EXCHANGE_ADDRESS, EXCHANGE_ABI, signer);

// Dat lenh mua (BUY_YES) va lenh ban (SELL_YES)
const bidPrice = ethers.parseUnits("0.48", 6);
const askPrice = ethers.parseUnits("0.52", 6);
const amount = ethers.parseUnits("100", 6);

// BUY_YES: nap USDC
await exchange.placeOrder(marketId, 0, bidPrice, amount); // side 0 = BUY_YES

// SELL_YES: nap token YES (phai co tu viec tach)
await exchange.placeOrder(marketId, 1, askPrice, amount); // side 1 = SELL_YES
```

## Quan ly chenh lech gia

| Giai doan thi truong | Chenh lech gia khuyen nghi |
| ------------ | ------------------ |
| Thi truong moi (khoi luong thap) | $0.05–$0.10 |
| Thi truong hoat dong | $0.02–$0.05 |
| Gan het han (< 24 gio) | $0.05–$0.10 hoac rong hon |

## Rui ro ton kho

Khi ban bi khop lenh mot phia nhieu hon phia kia, ban tich luy muc do pho nhiem theo huong:

- Qua nhieu token YES → vi the cua ban co lai neu YES thang
- Qua nhieu token NO → vi the cua ban co lai neu NO thang

Quan ly bang cach dieu chinh gia hoac su dung split/merge de tai can bang.

## Bot Tao lap Thi truong

PrediX cung cap mot ban tham chieu cua Bot Tao lap Thi truong. Xem kho luu tru GitHub de biet chi tiet.

---

**Tiep theo**: [Tong quan Thanh khoan](overview.md) · [Lenh gioi han](../trading/limit-orders.md)
