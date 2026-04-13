---
description: Đặt và quản lý lệnh giới hạn trên CLOB trên chuỗi
---

# Lệnh giới hạn

Lệnh giới hạn được đặt trực tiếp trên PrediXExchange (CLOB). Chúng tồn tại trên chuỗi cho đến khi được khớp, hủy, hoặc thị trường hết hạn.

## Đặt lệnh

```typescript
const exchange = new ethers.Contract(
  "0xa202abCb2A358c0862B2dA76b553398339F2C638",
  EXCHANGE_ABI,
  signer
);

// Enum Side: 0=BUY_YES, 1=SELL_YES, 2=BUY_NO, 3=SELL_NO
const side = 0; // BUY_YES
const price = ethers.parseUnits("0.65", 6);   // $0.65 mỗi token
const amount = ethers.parseUnits("100", 6);    // 100 token

// Với lệnh MUA: phê duyệt USDC cho Exchange
// deposit = price × amount / 1e6
await usdc.approve(exchange.target, price * amount / 1000000n);

const tx = await exchange.placeOrder(marketId, side, price, amount);
const receipt = await tx.wait();
// Phân tích sự kiện OrderPlaced để lấy orderId
```

## Hủy lệnh

```typescript
await exchange.cancelOrder(orderId);
// Tiền nạp chưa khớp được trả lại cho chủ lệnh
```

> ⚠️ Bất kỳ ai cũng có thể hủy lệnh thị trường đã hết hạn (sau endTime).

## Ràng buộc

| Tham số | Giá trị |
| ------- | ------- |
| Phạm vi giá | $0.01 – $0.99 (`10000` – `990000` với 6 chữ số thập phân) |
| Bước giá | $0.01 (`10000`) |
| Số lượng lệnh tối thiểu | $1.00 (`1000000` với 6 chữ số thập phân) |
| Số lệnh tối đa mỗi người dùng mỗi thị trường | 50 |
| Số lần khớp tối đa mỗi lần đặt | 20 |
| Ngăn chặn tự giao dịch | maker.owner ≠ taker.owner |

## Enum Side

```solidity
enum Side {
    BUY_YES,   // 0
    SELL_YES,  // 1
    BUY_NO,    // 2
    SELL_NO    // 3
}
```

---

**Tiếp theo**: [Công cụ khớp lệnh](matching-engine.md) · [Định tuyến thông minh](smart-routing.md) · [Sổ lệnh](order-book.md)
