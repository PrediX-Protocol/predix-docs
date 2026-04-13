---
description: Uniswap v4 Hook — phí động, chống tấn công sandwich, quản lý pool
---

# Hợp đồng Hook

PrediXHookV1 là một Uniswap v4 Hook bổ sung logic chuyên biệt cho thị trường dự đoán vào các pool AMM.

## Quyền Hook

| Điểm Hook | Kích hoạt | Mục đích |
| ---------- | ------- | ------- |
| `beforeInitialize` | ✅ | Xác thực đăng ký pool |
| `afterInitialize` | ✅ | Thiết lập metadata pool |
| `beforeSwap` | ✅ | Phí động + kiểm tra chống sandwich |
| `afterSwap` | ✅ | Ghi dữ liệu swap, phát sự kiện |

## Tính phí động

Phí tăng khi thị trường gần hết hạn để bảo vệ LP khỏi các nhà giao dịch có thông tin:

| Thời gian còn lại | Phí |
| -------------- | --- |
| > 7 ngày | 0.5% |
| 3–7 ngày | 1.0% |
| 1–3 ngày | 2.0% |
| < 24 giờ | 5.0% |

## Chống tấn công Sandwich

Hook theo dõi mỗi swap dưới dạng `(block.number, direction)` theo từng người dùng và pool. Nếu cùng một người dùng swap ngược chiều trong cùng một block, giao dịch sẽ bị hoàn lại với lỗi `SandwichDetected()`.

**Router đáng tin cậy**: Hook trích xuất địa chỉ người dùng thực tế từ `hookData` thay vì sử dụng `msg.sender`, giúp Router có thể thực thi thay mặt người dùng mà không gây ra cảnh báo giả.

## Các hàm

```solidity
// Chỉ Diamond gọi được
registerMarketPool(bytes32 marketId, address yesToken, PoolKey key)
setTrustedRouter(address router, bool trusted)

// Gọi một lần khi triển khai
initialize(address diamond, address usdc, address admin)

// Truy vấn
getMarketForPool(PoolId poolId) → bytes32 marketId
```

---

**Tiếp theo**: [Oracle](oracle.md) · [Phí](../concepts/fees.md) · [An toàn](safety.md)
