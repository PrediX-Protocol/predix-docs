---
description: Token YES và NO — tiêu chuẩn ERC-20 với khả năng kết hợp DeFi đầy đủ
---

# Token kết quả

## Tổng quan

Mỗi thị trường PrediX tạo ra hai token kết quả **ERC-20**:

| Token | Trả $1.00 nếu... | Trả $0.00 nếu... |
| ----- | ----------------- | ----------------- |
| **YES** | Sự kiện xảy ra | Sự kiện không xảy ra |
| **NO** | Sự kiện không xảy ra | Sự kiện xảy ra |

## Thông số kỹ thuật

| Thuộc tính | Giá trị |
| ---------- | ------- |
| Tiêu chuẩn | ERC-20 + ERC-2612 (Permit) |
| Số thập phân | 6 (giống USDC) |
| Quyền phát hành | Chỉ hợp đồng Diamond (bộ điều chỉnh `onlyFactory`) |
| Quyền đốt | Chỉ hợp đồng Diamond |
| Trường bất biến | `factory`, `marketId`, `isYesToken` |

## ERC-2612 Permit

Token kết quả hỗ trợ phê duyệt không gas thông qua hàm `permit`. Điều này cho phép người dùng phê duyệt và chuyển trong một giao dịch duy nhất sử dụng chữ ký ngoại chuỗi.

```typescript
// Ký permit ngoại chuỗi
const deadline = Math.floor(Date.now() / 1000) + 3600;
const nonce = await yesToken.nonces(userAddress);
const signature = await signer.signTypedData(domain, types, { owner, spender, value, nonce, deadline });

// Thực thi permit trên chuỗi (không cần approve trước)
await yesToken.permit(owner, spender, value, deadline, v, r, s);
```

## Bất biến cốt lõi

```
YES.totalSupply == NO.totalSupply == market.totalCollateral
```

Bất biến này được duy trì qua **tất cả** các thao tác: `splitPosition`, `mergePositions`, `groupSplit`, `groupMerge`, `redeemMarketTokens` và `refund`.

## Tạo địa chỉ

Địa chỉ token YES phải nhỏ hơn địa chỉ USDC (yêu cầu sắp xếp pool Uniswap v4). PrediX sử dụng **khai thác salt CREATE2** để tìm địa chỉ phù hợp một cách xác định.

> ⚠️ **Quan trọng**: Token kết quả CHỈ có thể được phát hành và đốt bởi hợp đồng Diamond. Không có hàm phát hành công khai.

---

**Tiếp theo**: [Tách & Gộp](split-and-merge.md) · [Phí](fees.md) · [Tổng quan giao dịch](../trading/overview.md)
