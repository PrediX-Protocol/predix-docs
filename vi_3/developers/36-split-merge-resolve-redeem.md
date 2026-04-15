# 36. Split, Merge, Resolve & Redeem

## Split Position
// Approve USDC to Diamond
await usdc.approve(DIAMOND_ADDRESS, ethers.parseUnits("100", 6));

// Split: 100 USDC → 100 YES + 100 NO
await diamond.splitPosition(marketId, ethers.parseUnits("100", 6));

## Merge Positions
// Approve YES and NO tokens to Diamond
await yesToken.approve(DIAMOND_ADDRESS, ethers.parseUnits("50", 6));
await noToken.approve(DIAMOND_ADDRESS, ethers.parseUnits("50", 6));

// Merge: 50 YES + 50 NO → 50 USDC
await diamond.mergePositions(marketId, ethers.parseUnits("50", 6));

## Resolve Market
// After oracle has resolved
await diamond.resolveMarket(marketId);

## Redeem Tokens
// After market resolved -winning tokens → USDC
const tx = await diamond.redeemMarketTokens(marketId);
