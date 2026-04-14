# Developer Quickstart

Build trên PrediX trong chưa đầy 10 phút. Hướng dẫn này gồm kết nối, đọc dữ liệu market và thực hiện giao dịch đầu tiên.

## Yêu cầu

- Node.js 18+
- ethers.js v6 (`npm install ethers`)
- TypeScript (khuyến nghị)

## Kết nối Unichain Sepolia

```typescript
import { ethers } from "ethers";

const provider = new ethers.JsonRpcProvider("https://sepolia.unichain.org");
const signer = new ethers.Wallet("YOUR_PRIVATE_KEY", provider);

// Verify kết nối
const chainId = (await provider.getNetwork()).chainId;
console.log(`Đã kết nối chain ${chainId}`); // 1301
```

Địa chỉ contract và ABI xem tại [Tổng quan contracts](../contracts/overview.md).

## Đọc một market

```typescript
const DIAMOND = "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A";

const diamond = new ethers.Contract(DIAMOND, [
  "function getMarket(bytes32) view returns (tuple(string question, string description, uint256 endTime, address yesToken, address noToken, bytes32 poolId, bytes32 categoryId, address oracle, bool isResolved, bool outcome, uint256 totalCollateral, bytes32 questionHash, uint256 resolvedAt))"
], provider);

const market = await diamond.getMarket(marketId);
console.log(`Câu hỏi: ${market.question}`);
console.log(`Hết hạn: ${new Date(Number(market.endTime) * 1000)}`);
console.log(`Đã resolve: ${market.isResolved}`);
console.log(`Collateral: ${ethers.formatUnits(market.totalCollateral, 6)} USDC`);
```

## Thực hiện giao dịch đầu tiên

```typescript
const ROUTER = "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7";
const USDC = "0x12fd156C8b5F2901BA2781d97db84AaC56b2b911";

const usdc = new ethers.Contract(USDC, [
  "function approve(address,uint256)",
  "function balanceOf(address) view returns (uint256)"
], signer);

const router = new ethers.Contract(ROUTER, [
  "function buyYes(bytes32,uint256,uint256,address,uint256) returns (uint256)",
  "function quoteBuyYes(bytes32,uint256) view returns (uint256)"
], signer);

// 1. Kiểm tra balance
const balance = await usdc.balanceOf(signer.address);
console.log(`Số dư USDC: ${ethers.formatUnits(balance, 6)}`);

// 2. Lấy quote
const usdcIn = ethers.parseUnits("10", 6);
const expectedYes = await router.quoteBuyYes(marketId, usdcIn);
console.log(`Dự kiến nhận: ${ethers.formatUnits(expectedYes, 6)} YES`);

// 3. Approve USDC
await (await usdc.approve(ROUTER, usdcIn)).wait();

// 4. Mua YES với slippage 5%
const minOut = expectedYes * 95n / 100n;
const deadline = Math.floor(Date.now() / 1000) + 300;

const tx = await router.buyYes(marketId, usdcIn, minOut, signer.address, deadline);
const receipt = await tx.wait();
console.log(`Giao dịch OK: ${receipt.hash}`);
```

## Lấy USDC test

Dùng [Unichain Sepolia Faucet](https://faucet.unichain.org) hoặc tương tác trực tiếp với contract test USDC để mint token.

## Tiếp theo

- [ABIs](abis.md) — tải file ABI
- [Tạo Market](create-market.md) — deploy market của riêng bạn
- [Tích hợp giao dịch](trading-integration.md) — hướng dẫn code đầy đủ
- [Events](events.md) — nghe event on-chain
