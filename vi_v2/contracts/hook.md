# Hook Contract

Hook là một Uniswap v4 Hook cung cấp phần AMM của PrediX, cài đặt logic tuỳ biến tại các điểm trong vòng đời pool.

## Hook permissions

| Hook point | Bật | Mục đích |
|------------|---------|---------|
| `beforeInitialize` | ✅ | Kiểm tra đăng ký pool |
| `afterInitialize` | ✅ | Lưu ánh xạ pool-market |
| `beforeSwap` | ✅ | Tính phí động, anti-sandwich |
| `afterSwap` | ✅ | Emit event, cập nhật metric |

## Tính phí động

Hook ghi đè phí mặc định của Uniswap v4 ở `beforeSwap` dựa trên thời gian còn lại tới endTime:

```
nếu còn > 7 ngày   → phí = 0.5%
nếu còn > 3 ngày   → phí = 1.0%
nếu còn > 1 ngày   → phí = 2.0%
nếu còn < 24 giờ   → phí = 5.0%
```

Xem [Phí](../concepts/fees.md) cho biểu phí đầy đủ và lý do.

## Bảo vệ chống sandwich

- **Unichain Flashblocks (200ms):** Block cực nhanh giảm cửa sổ MEV
- **TEE ordering:** Trusted Execution Environment của Unichain sắp xếp giao dịch công bằng
- **Phí động gần expiry:** Phí 5% làm sandwich attack không có lời

## Hàm

### Registration (Chỉ Diamond)

```solidity
registerMarketPool(bytes32 marketId, address yesToken, PoolKey key)
```

Diamond gọi khi `createMarket()` để đăng ký pool YES/USDC. Chỉ Diamond được phép gọi.

### Cấu hình (Chỉ Diamond)

```solidity
setTrustedRouter(address router, bool trusted)
```

Chỉ Router được tin cậy mới thực thi swap qua Hook. Ngăn truy cập trái phép.

### Khởi tạo (1 lần)

```solidity
initialize(address diamond, address usdc, address admin)
```

Setup ban đầu liên kết Hook với Diamond và USDC.

### View

```solidity
getMarketForPool(PoolId poolId) → bytes32 marketId
```

## Tiếp theo

- [Oracle](oracle.md) — resolution adapter
- [Router](router.md) — cách Router gọi Hook
- [Thanh khoản](../liquidity/overview.md) — cung cấp LP cho AMM
