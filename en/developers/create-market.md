---
description: Create a prediction market programmatically
---

# Create Market

> ⚠️ Market creation requires **ADMIN_ROLE**.

## Step by Step

```typescript
import { ethers } from "ethers";

const diamond = new ethers.Contract(DIAMOND_ADDRESS, DIAMOND_ABI, signer);

// Parameters
const question = "Will BTC exceed $100,000 by December 31, 2026?";
const description = "Resolves YES if Bitcoin price (Chainlink BTC/USD) >= $100,000.";
const endTime = Math.floor(new Date("2026-12-31T23:59:59Z").getTime() / 1000);
const categoryId = ethers.ZeroHash; // No category (standalone market)
const oracle = "0x699A8C74663b1C852E195b2ffa00D5965E992Cf3"; // ManualOracle

// Create
const tx = await diamond.createMarket(question, description, endTime, categoryId, oracle);
const receipt = await tx.wait();

// Parse MarketCreated event
const event = receipt.logs.find(
  (log) => diamond.interface.parseLog(log)?.name === "MarketCreated"
);
const parsed = diamond.interface.parseLog(event);
const marketId = parsed.args.marketId;

console.log("Market ID:", marketId);
console.log("YES Token:", parsed.args.yesToken);
console.log("NO Token:", parsed.args.noToken);
```

## What Happens Automatically

When `createMarket` is called:

1. YES and NO ERC-20 tokens are deployed (CREATE2)
2. Uniswap v4 AMM pool (YES/USDC) is created and initialized at $0.50
3. Pool is registered with the Hook
4. Market data is stored in Diamond storage

## Parameters

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| `question` | string | The market question |
| `description` | string | Additional context |
| `endTime` | uint256 | Unix timestamp when trading stops |
| `categoryId` | bytes32 | Category ID (or `bytes32(0)` for standalone) |
| `oracle` | address | Approved oracle adapter address |

## Verify On-Chain

```typescript
const market = await diamond.getMarket(marketId);
console.log("Exists:", await diamond.isMarketExists(marketId));
console.log("Oracle:", market.oracle);
console.log("End Time:", new Date(Number(market.endTime) * 1000));
```

---

**Next**: [Trading Integration](trading-integration.md) · [Split & Merge](split-merge.md)
