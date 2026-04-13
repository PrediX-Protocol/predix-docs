---
description: Start building on PrediX in 5 minutes
---

# Developer Quickstart

## Prerequisites

- Node.js 18+
- ethers.js v6

## Setup

```bash
npm install ethers
```

## Connect to Unichain Sepolia

```typescript
import { ethers } from "ethers";

const provider = new ethers.JsonRpcProvider("https://sepolia.unichain.org");
const signer = new ethers.Wallet(process.env.PRIVATE_KEY!, provider);

// Contract instances
const DIAMOND = "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A";
const EXCHANGE = "0xa202abCb2A358c0862B2dA76b553398339F2C638";
const ROUTER = "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7";
const USDC = "0x12fd156C8b5F2901BA2781d97db84AaC56b2b911";

const diamond = new ethers.Contract(DIAMOND, DIAMOND_ABI, signer);
const router = new ethers.Contract(ROUTER, ROUTER_ABI, signer);
const usdc = new ethers.Contract(USDC, ERC20_ABI, signer);
```

## Read a Market

```typescript
const marketId = "0x..."; // Get from MarketCreated event or API
const market = await diamond.getMarket(marketId);

console.log("Question:", market.question);
console.log("End time:", new Date(Number(market.endTime) * 1000));
console.log("YES token:", market.yesToken);
console.log("NO token:", market.noToken);
console.log("Resolved:", market.isResolved);
console.log("Collateral:", ethers.formatUnits(market.totalCollateral, 6), "USDC");
```

## Place First Trade

```typescript
const usdcAmount = ethers.parseUnits("10", 6); // 10 USDC
const deadline = Math.floor(Date.now() / 1000) + 300; // 5 min

// Step 1: Get quote
const expectedYes = await router.quoteBuyYes(marketId, usdcAmount);
const minYesOut = expectedYes * 95n / 100n; // 5% slippage

// Step 2: Approve USDC
await (await usdc.approve(ROUTER, usdcAmount)).wait();

// Step 3: Buy YES tokens
const tx = await router.buyYes(marketId, usdcAmount, minYesOut, signer.address, deadline);
const receipt = await tx.wait();

console.log("Trade hash:", receipt.hash);
```

---

**Next**: [Contract Addresses](addresses.md) · [ABIs](abis.md) · [Trading Integration](trading-integration.md)
