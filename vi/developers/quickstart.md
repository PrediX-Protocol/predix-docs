---
description: Bắt đầu phát triển trên PrediX trong 5 phút
---

# Hướng dẫn nhanh cho nhà phát triển

## Yêu cầu

- Node.js 18+
- ethers.js v6

## Cài đặt

```bash
npm install ethers
```

## Kết nối với Unichain Sepolia

```typescript
import { ethers } from "ethers";

const provider = new ethers.JsonRpcProvider("https://sepolia.unichain.org");
const signer = new ethers.Wallet(process.env.PRIVATE_KEY!, provider);

// Các instance hợp đồng
const DIAMOND = "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A";
const EXCHANGE = "0xa202abCb2A358c0862B2dA76b553398339F2C638";
const ROUTER = "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7";
const USDC = "0x12fd156C8b5F2901BA2781d97db84AaC56b2b911";

const diamond = new ethers.Contract(DIAMOND, DIAMOND_ABI, signer);
const router = new ethers.Contract(ROUTER, ROUTER_ABI, signer);
const usdc = new ethers.Contract(USDC, ERC20_ABI, signer);
```

## Đọc thông tin thị trường

```typescript
const marketId = "0x..."; // Lấy từ sự kiện MarketCreated hoặc API
const market = await diamond.getMarket(marketId);

console.log("Câu hỏi:", market.question);
console.log("Thời gian kết thúc:", new Date(Number(market.endTime) * 1000));
console.log("Token YES:", market.yesToken);
console.log("Token NO:", market.noToken);
console.log("Đã giải quyết:", market.isResolved);
console.log("Tài sản thế chấp:", ethers.formatUnits(market.totalCollateral, 6), "USDC");
```

## Thực hiện giao dịch đầu tiên

```typescript
const usdcAmount = ethers.parseUnits("10", 6); // 10 USDC
const deadline = Math.floor(Date.now() / 1000) + 300; // 5 phút

// Bước 1: Lấy báo giá
const expectedYes = await router.quoteBuyYes(marketId, usdcAmount);
const minYesOut = expectedYes * 95n / 100n; // 5% trượt giá

// Bước 2: Phê duyệt USDC
await (await usdc.approve(ROUTER, usdcAmount)).wait();

// Bước 3: Mua token YES
const tx = await router.buyYes(marketId, usdcAmount, minYesOut, signer.address, deadline);
const receipt = await tx.wait();

console.log("Hash giao dịch:", receipt.hash);
```

---

**Tiếp theo**: [Địa chỉ hợp đồng](addresses.md) · [ABI](abis.md) · [Tích hợp giao dịch](trading-integration.md)
