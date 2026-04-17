---
description: 5분 안에 PrediX 개발 시작하기
---

# Developer Quickstart

## Prerequisites

* Node.js 18+
* ethers.js v6
* TypeScript (권장)

### 설치

```bash
npm install ethers
```

### Connect to Unichain Sepolia

```typescript
import { ethers } from "ethers";

const provider = new ethers.JsonRpcProvider("https://sepolia.unichain.org");
const wallet = new ethers.Wallet(PRIVATE_KEY, provider);

// Chain ID: 1301
```

### Read a Market

```typescript
const diamond = new ethers.Contract(DIAMOND_ADDRESS, MarketFacetABI, provider);
const market = await diamond.getMarket(marketId);
console.log("Question:", market.question);
console.log("End Time:", new Date(Number(market.endTime) * 1000));
console.log("Total Collateral:", ethers.formatUnits(market.totalCollateral, 6));
```

### Place First Trade

```typescript
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20ABI, wallet);
const router = new ethers.Contract(ROUTER_ADDRESS, RouterABI, wallet);

// Approve USDC
await usdc.approve(ROUTER_ADDRESS, ethers.parseUnits("10", 6));

// Buy YES tokens
const deadline = Math.floor(Date.now() / 1000) + 300; // 5 min
const tx = await router.buyYes(
  marketId,
  ethers.parseUnits("10", 6),  // 10 USDC
  ethers.parseUnits("8", 6),   // min 8 YES out
  wallet.address,
  deadline
);
await tx.wait();
```

### Contract Addresses

```
DIAMOND = 0xF38a265E6e4F57D000a1CC08004da5B4A380B08A
HOOK = 0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0
EXCHANGE = 0xa202abCb2A358c0862B2dA76b553398339F2C638
ROUTER = 0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7
ORACLE = 0x699A8C74663b1C852E195b2ffa00D5965E992Cf3
USDC = 0x12fd156C8b5F2901BA2781d97db84AaC56b2b911
POOL_MANAGER = 0x00B036B58a818B1BC34d502D3fE730Db729e62AC
```
