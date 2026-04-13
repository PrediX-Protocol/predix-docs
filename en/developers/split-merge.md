---
description: Split USDC into tokens and merge tokens back
---

# Split & Merge (Developer Guide)

## Split Position

```typescript
const diamond = new ethers.Contract(DIAMOND_ADDRESS, DIAMOND_ABI, signer);
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, signer);

const marketId = "0x...";
const amount = ethers.parseUnits("100", 6); // 100 USDC

// Approve USDC to Diamond
await (await usdc.approve(DIAMOND_ADDRESS, amount)).wait();

// Split: 100 USDC → 100 YES + 100 NO
const tx = await diamond.splitPosition(marketId, amount);
await tx.wait();

// Verify balances
const market = await diamond.getMarket(marketId);
const yesToken = new ethers.Contract(market.yesToken, ERC20_ABI, provider);
const noToken = new ethers.Contract(market.noToken, ERC20_ABI, provider);

console.log("YES balance:", ethers.formatUnits(await yesToken.balanceOf(signer.address), 6));
console.log("NO balance:", ethers.formatUnits(await noToken.balanceOf(signer.address), 6));
```

## Merge Positions

```typescript
const mergeAmount = ethers.parseUnits("50", 6); // Merge 50 YES + 50 NO

// No approval needed (Diamond already has burn authority)
const tx = await diamond.mergePositions(marketId, mergeAmount);
await tx.wait();

// Result: 50 USDC returned to your wallet
```

> ⚠️ You must hold **equal** amounts of YES and NO tokens to merge. The merge amount cannot exceed your balance of either token.

---

**Next**: [Resolve & Redeem](resolve-redeem.md) · [Events](events.md)
