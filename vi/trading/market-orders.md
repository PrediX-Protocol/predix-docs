---
description: Thực hiện lệnh thị trường thông qua Smart Router
---

# Lệnh thị trường

Lệnh thị trường được thực hiện ngay lập tức thông qua Router, tổng hợp thanh khoản từ cả CLOB và AMM.

## Các hàm

| Hàm | Hành động |
| --- | --------- |
| `buyYes(marketId, usdcIn, minYesOut, recipient, deadline)` | Trả USDC, nhận token YES |
| `sellYes(marketId, yesIn, minUsdcOut, recipient, deadline)` | Trả token YES, nhận USDC |
| `buyNo(marketId, usdcIn, mintAmount, minNoOut, recipient, deadline)` | Trả USDC, nhận token NO |
| `sellNo(marketId, noIn, minUsdcOut, recipient, deadline)` | Trả token NO, nhận USDC |

## Ví dụ mã

### Mua YES

```typescript
import { ethers } from "ethers";

const router = new ethers.Contract(
  "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7",
  ROUTER_ABI,
  signer
);
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, signer);

const marketId = "0x...";
const usdcIn = ethers.parseUnits("100", 6);    // 100 USDC
const minYesOut = ethers.parseUnits("90", 6);   // 2% trượt giá
const deadline = Math.floor(Date.now() / 1000) + 300; // 5 phút

// Bước 1: Phê duyệt USDC cho Router
await usdc.approve(router.target, usdcIn);

// Bước 2: Mua YES
const tx = await router.buyYes(marketId, usdcIn, minYesOut, signer.address, deadline);
const receipt = await tx.wait();
console.log("Giao dịch đã thực hiện:", receipt.hash);
```

### Bán YES

```typescript
const yesToken = new ethers.Contract(market.yesToken, ERC20_ABI, signer);
const yesIn = ethers.parseUnits("100", 6);
const minUsdcOut = ethers.parseUnits("60", 6);

// Phê duyệt token YES cho Router
await yesToken.approve(router.target, yesIn);

// Bán YES
await router.sellYes(marketId, yesIn, minUsdcOut, signer.address, deadline);
```

### Mua NO

```typescript
// Mua NO yêu cầu tham số mintAmount (được sử dụng nội bộ cho tách)
const usdcIn = ethers.parseUnits("100", 6);
const mintAmount = ethers.parseUnits("100", 6); // Số lượng cần tách
const minNoOut = ethers.parseUnits("90", 6);

await usdc.approve(router.target, usdcIn);
await router.buyNo(marketId, usdcIn, mintAmount, minNoOut, signer.address, deadline);
```

## Truy vấn giá trước khi giao dịch

Luôn lấy báo giá trước khi thực hiện:

```typescript
const expectedYes = await router.quoteBuyYes(marketId, usdcIn);
console.log("YES dự kiến:", ethers.formatUnits(expectedYes, 6));

// Đặt minOut với dung sai trượt giá
const minYesOut = expectedYes * 98n / 100n; // 2% trượt giá
```

---

**Tiếp theo**: [Lệnh giới hạn](limit-orders.md) · [Định tuyến thông minh](smart-routing.md) · [Tích hợp giao dịch cho nhà phát triển](../developers/trading-integration.md)
