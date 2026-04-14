# Lệnh giới hạn

Lệnh giới hạn cho phép bạn chỉ định mức giá cụ thể muốn giao dịch. Lệnh nằm chờ trên sổ CLOB on-chain đến khi được khớp hoặc bị huỷ.

## Hàm

Mọi hàm limit order nằm trên **Exchange** (`0xa202abCb2A358c0862B2dA76b553398339F2C638`).

```solidity
placeOrder(bytes32 marketId, uint8 side, uint256 price, uint256 amount)
  → (bytes32 orderId, uint256 filledAmount)

cancelOrder(bytes32 orderId)
```

## Enum side

| Giá trị | Side | Ý nghĩa |
|-------|------|---------|
| 0 | `BUY_YES` | Đặt bid cho YES |
| 1 | `SELL_YES` | Đặt ask cho YES |
| 2 | `BUY_NO` | Đặt bid cho NO |
| 3 | `SELL_NO` | Đặt ask cho NO |

## Format giá

Giá tính bằng USDC với 6 decimals, từ $0.01 đến $0.99:

| Giá hiển thị | Giá trị on-chain | Tick |
|---------------|---------------|------|
| $0.01 | `10000` | Min |
| $0.50 | `500000` | Mid |
| $0.99 | `990000` | Max |

## Ràng buộc

- **Lệnh tối thiểu:** $1.00 (1,000,000 ở 6 decimals)
- **Số lệnh tối đa / user / market:** 50
- **Bước giá:** chỉ $0.01

## Ví dụ code

```typescript
const EXCHANGE = "0xa202abCb2A358c0862B2dA76b553398339F2C638";

const exchange = new ethers.Contract(EXCHANGE, [
  "function placeOrder(bytes32,uint8,uint256,uint256) returns (bytes32,uint256)",
  "function cancelOrder(bytes32)"
], signer);

// Approve USDC cho Exchange cho lệnh mua
await usdc.approve(EXCHANGE, ethers.parseUnits("100", 6));

// Đặt lệnh: MUA 100 YES ở $0.55
const side = 0; // BUY_YES
const price = 550000; // $0.55 ở 6 decimals
const amount = ethers.parseUnits("100", 6);

const [orderId, filledAmount] = await exchange.placeOrder(marketId, side, price, amount);
console.log(`Lệnh ${orderId} đã đặt. Khớp ngay: ${filledAmount}`);

// Huỷ lệnh
await exchange.cancelOrder(orderId);
```

> ⚠️ `placeOrder` có thể khớp một phần ngay nếu có lệnh đối ứng. Hãy kiểm tra `filledAmount` trong kết quả trả về.

> ⚠️ Với market đã hết hạn, bất kỳ ai cũng có thể huỷ lệnh, không chỉ chủ lệnh.

## Tiếp theo

- [Matching Engine](matching-engine.md) — cách lệnh được khớp
- [Order Book](order-book.md) — đọc dữ liệu sổ lệnh
- [Lệnh thị trường](market-orders.md) — khớp tức thì qua Router
