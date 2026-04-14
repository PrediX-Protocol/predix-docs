# Market Making CLOB

Cung cấp thanh khoản trên sổ lệnh on-chain bằng cách đặt đồng thời bid và ask.

## Chiến lược cơ bản

Đặt lệnh mua dưới giá thị trường và lệnh bán trên giá thị trường, ăn phần spread.

```typescript
const EXCHANGE = "0xa202abCb2A358c0862B2dA76b553398339F2C638";

const exchange = new ethers.Contract(EXCHANGE, [
  "function placeOrder(bytes32,uint8,uint256,uint256) returns (bytes32,uint256)",
  "function cancelOrder(bytes32)"
], signer);

// Giá YES hiện tại ~$0.55
// Đặt bid ở $0.53, ask ở $0.57 → spread $0.04

// BUY_YES ở $0.53
await usdc.approve(EXCHANGE, ethers.parseUnits("1000", 6));
const [bidId] = await exchange.placeOrder(marketId, 0, 530000, ethers.parseUnits("100", 6));

// SELL_YES ở $0.57 (cần có YES token)
await yesToken.approve(EXCHANGE, ethers.parseUnits("100", 6));
const [askId] = await exchange.placeOrder(marketId, 1, 570000, ethers.parseUnits("100", 6));
```

## Quản lý spread

| Spread | Mức rủi ro | Dùng khi |
|--------|-----------|-------------|
| $0.01–$0.02 | Cao (chật) | Market volume cao, MM cạnh tranh |
| $0.03–$0.05 | Trung bình | Market tiêu chuẩn |
| $0.05–$0.10 | Thấp (rộng) | Market volume thấp hoặc biến động mạnh |

## Rủi ro tồn kho

Khi lệnh khớp, bạn sẽ tích luỹ vị thế theo hướng nào đó:

- Nhiều YES được khớp → bạn long YES (kỳ vọng sự kiện xảy ra)
- Nhiều NO được khớp → bạn long NO (kỳ vọng sự kiện không xảy ra)

**Khắc phục:** Thường xuyên rebalance bằng cách điều chỉnh size bid/ask, dùng split/merge, hoặc trade trên AMM.

## Ràng buộc

- **Tối đa 50 lệnh** mỗi user mỗi market — quản lý số lượng lệnh cẩn thận
- **Lệnh tối thiểu $1.00** — mỗi bên cần đủ min
- **Bước giá $0.01** — mọi giá phải đúng tick

## Bot market maker

Với MM tự động, xây bot làm:

1. Đọc `getBestPrices` và `getDepthAtPrice` để biết trạng thái hiện tại
2. Tính fair value từ tín hiệu ngoài
3. Đặt lệnh với spread cấu hình quanh fair value
4. Theo dõi fill qua event `OrderMatched`
5. Rebalance inventory khi vượt ngưỡng
6. Huỷ và thay thế các lệnh cũ theo chu kỳ

## Tiếp theo

- [Tổng quan thanh khoản](overview.md) — AMM LP
- [Order Book](../trading/order-book.md) — đọc depth CLOB
- [Lệnh giới hạn](../trading/limit-orders.md) — cơ chế order
