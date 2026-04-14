# Tổng quan thanh khoản

PrediX có hai kiểu cung cấp thanh khoản: AMM LP (thụ động) và market making trên CLOB (chủ động).

## Thanh khoản AMM (Uniswap v4)

Cung cấp thanh khoản cho pool YES/USDC trên Uniswap v4 và ăn phí swap động.

### Thu nhập từ phí

| Thời gian còn lại | Phí | Phần của bạn |
|----------------|----------|------------|
| > 7 ngày | 0.5% | Chia tỷ lệ theo LP |
| 3–7 ngày | 1.0% | Chia tỷ lệ theo LP |
| 1–3 ngày | 2.0% | Chia tỷ lệ theo LP |
| < 24 giờ | 5.0% | Chia tỷ lệ theo LP |

Phí tăng dần gần expiry để bù cho rủi ro thông tin bất đối xứng mà LP phải gánh. Xem [Phí](../concepts/fees.md).

### Cách cung cấp AMM LP

#### Bước 1: Có token YES

Split USDC thành YES + NO qua Diamond. Xem [Split & Merge](../concepts/split-and-merge.md).

```typescript
// Split $1000 USDC → 1000 YES + 1000 NO
await usdc.approve(DIAMOND, ethers.parseUnits("1000", 6));
await diamond.splitPosition(marketId, ethers.parseUnits("1000", 6));
```

#### Bước 2: Approve token cho Position Manager

```typescript
const yesToken = new ethers.Contract(market.yesToken, ["function approve(address,uint256)"], signer);
await yesToken.approve(POSITION_MANAGER, ethers.MaxUint256);
await usdc.approve(POSITION_MANAGER, ethers.MaxUint256);
```

#### Bước 3: Mint LP position

Dùng PositionManager của Uniswap v4 để thêm thanh khoản concentrated hoặc full-range.

### Concentrated vs Full-Range

| Chiến lược | Ưu | Nhược |
|----------|------|------|
| **Concentrated** ($0.30–$0.70) | Ăn nhiều phí hơn, hiệu quả vốn | Phải rebalance, IL cao hơn |
| **Full-range** ($0.01–$0.99) | Không cần rebalance | Phí ít, kém hiệu quả vốn |

### Rủi ro

- **Impermanent loss:** Nếu giá biến động mạnh, vị thế của bạn có thể tệ hơn việc chỉ hold
- **Toxic flow gần expiry:** Trader có thông tin giao dịch quyết liệt trước khi resolve, LP gánh lỗ
- **Rủi ro phân xử:** Nếu không rút LP trước khi resolve, token bên thắng sẽ redeem tự động

> ⚠️ **Hãy rút LP trước khi market hết hạn.** Sau khi hết hạn, pool AMM có thể thiếu thanh khoản và khó thoát.

### Best practice

1. LP ở market còn > 7 ngày (phí thấp, toxic flow thấp)
2. Dùng concentrated range quanh xác suất hiện tại
3. Theo dõi và rút 24–48 giờ trước khi expiry
4. Rebalance khi giá ra khỏi range

## Tiếp theo

- [Market Making](market-making.md) — cung cấp thanh khoản CLOB
- [Phí](../concepts/fees.md) — biểu phí chi tiết
- [Split & Merge](../concepts/split-and-merge.md) — cách có YES token để LP
