# 36. Split, Merge, Resolve & Redeem

## Split Position

```typescript
// Diamond에 USDC approve
await usdc.approve(DIAMOND_ADDRESS, ethers.parseUnits("100", 6));

// Split: 100 USDC → 100 YES + 100 NO
await diamond.splitPosition(marketId, ethers.parseUnits("100", 6));
```

## Merge Positions

```typescript
// Diamond에 YES, NO 토큰 approve
await yesToken.approve(DIAMOND_ADDRESS, ethers.parseUnits("50", 6));
await noToken.approve(DIAMOND_ADDRESS, ethers.parseUnits("50", 6));

// Merge: 50 YES + 50 NO → 50 USDC
await diamond.mergePositions(marketId, ethers.parseUnits("50", 6));
```

## Resolve Market

```typescript
// Oracle이 resolve한 후
await diamond.resolveMarket(marketId);
```

## Redeem Tokens

```typescript
// Market resolve 후 — 올바른 토큰 → USDC
const tx = await diamond.redeemMarketTokens(marketId);
```
