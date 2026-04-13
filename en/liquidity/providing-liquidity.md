---
description: Step-by-step guide to providing AMM liquidity
---

# Providing Liquidity

## Overview

AMM liquidity is provided to the Uniswap v4 YES/USDC pool via the PositionManager.

## Steps

### 1. Split USDC into YES + NO

```typescript
const amount = ethers.parseUnits("1000", 6); // 1000 USDC
await usdc.approve(DIAMOND_ADDRESS, amount);
await diamond.splitPosition(marketId, amount);
// Now you have 1000 YES + 1000 NO
```

### 2. Approve YES and USDC to PositionManager

```typescript
const yesToken = new ethers.Contract(market.yesToken, ERC20_ABI, signer);
await yesToken.approve(POSITION_MANAGER, ethers.MaxUint256);
await usdc.approve(POSITION_MANAGER, ethers.MaxUint256);
```

### 3. Mint LP Position

Use the Uniswap v4 PositionManager to create a liquidity position in the YES/USDC pool. You can choose:

- **Full-range**: Covers $0.01–$0.99, less capital efficient
- **Concentrated**: Covers a narrow range (e.g., $0.40–$0.70), more capital efficient

### 4. When to Remove

- Remove LP **at least 24 hours** before market expiry (when fees jump to 5%)
- Monitor the market — if outcome becomes certain early, remove immediately
- After resolution, the losing token is worth $0

> ⚠️ **Note**: The NO tokens from split can be sold or held separately. They are not needed for LP.

---

**Next**: [Market Making](market-making.md) · [Liquidity Overview](overview.md)
