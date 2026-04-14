# Create a Market

Markets can only be created by addresses with `ADMIN_ROLE` on the Diamond contract.

## Step by Step

### 1. Prepare Parameters

```typescript
const question = "Will ETH surpass $10,000 by December 31, 2026?";
const description = "Resolves YES if ETH/USD price exceeds $10,000 on any major exchange before Dec 31, 2026 23:59 UTC.";
const endTime = Math.floor(new Date("2026-12-31T23:59:00Z").getTime() / 1000);
const categoryId = ethers.ZeroHash; // No category (standalone market)
const oracle = "0x699A8C74663b1C852E195b2ffa00D5965E992Cf3"; // ManualOracleAdapter
```

### 2. Create Market

```typescript
const DIAMOND = "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A";

const diamond = new ethers.Contract(DIAMOND, [
  "function createMarket(string,string,uint256,bytes32,address) returns (bytes32)"
], signer);

const tx = await diamond.createMarket(question, description, endTime, categoryId, oracle);
const receipt = await tx.wait();

// Extract marketId from MarketCreated event
const event = receipt.logs.find((log: any) => {
  try {
    return diamond.interface.parseLog(log)?.name === "MarketCreated";
  } catch { return false; }
});
const marketId = diamond.interface.parseLog(event).args.marketId;
console.log(`Market created: ${marketId}`);
```

### 3. What Happens Automatically

When `createMarket` executes, the Diamond:

1. Deploys YES and NO ERC-20 token contracts
2. Registers the YES/USDC pool on the Hook via `registerMarketPool`
3. Initializes the Uniswap v4 pool
4. Emits `MarketCreated` event

The market is immediately tradeable — no additional setup required.

### 4. Verify On-Chain

```typescript
const market = await diamond.getMarket(marketId);
console.log(`YES token: ${market.yesToken}`);
console.log(`NO token: ${market.noToken}`);
console.log(`Oracle: ${market.oracle}`);
```

> ⚠️ Only approved oracles can be assigned. Use `setApprovedOracle` first if using a new oracle adapter.

## Creating a Category

```typescript
const tx = await diamond.createCategory(
  "US Presidential Election 2028",
  "Which candidate will win the 2028 US Presidential Election?",
  endTime
);
// Then add markets to the category:
await diamond.addMarketToCategory(categoryId, marketId);
```

## Next Steps

- [Trading Integration](trading-integration.md) — trade on your new market
- [Resolve & Redeem](resolve-redeem.md) — resolve when the market ends
- [Contract: Oracle](../contracts/oracle.md) — oracle setup
