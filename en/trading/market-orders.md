---
description: Execute market orders through the Smart Router
---

# Market Orders

Market orders execute immediately through the Router, which aggregates liquidity from both CLOB and AMM.

## Functions

| Function | Action |
| -------- | ------ |
| `buyYes(marketId, usdcIn, minYesOut, recipient, deadline)` | Pay USDC, receive YES tokens |
| `sellYes(marketId, yesIn, minUsdcOut, recipient, deadline)` | Pay YES tokens, receive USDC |
| `buyNo(marketId, usdcIn, mintAmount, minNoOut, recipient, deadline)` | Pay USDC, receive NO tokens |
| `sellNo(marketId, noIn, minUsdcOut, recipient, deadline)` | Pay NO tokens, receive USDC |

## Code Examples

### Buy YES

```typescript
import { ethers } from "ethers";

const router = new ethers.Contract(
  "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7",
  ROUTER_ABI,
  signer
);
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, signer);

const marketId = "0x...";
const usdcIn = ethers.parseUnits("100", 6);    // 100 USDC
const minYesOut = ethers.parseUnits("90", 6);   // 2% slippage
const deadline = Math.floor(Date.now() / 1000) + 300; // 5 minutes

// Step 1: Approve USDC to Router
await usdc.approve(router.target, usdcIn);

// Step 2: Buy YES
const tx = await router.buyYes(marketId, usdcIn, minYesOut, signer.address, deadline);
const receipt = await tx.wait();
console.log("Trade executed:", receipt.hash);
```

### Sell YES

```typescript
const yesToken = new ethers.Contract(market.yesToken, ERC20_ABI, signer);
const yesIn = ethers.parseUnits("100", 6);
const minUsdcOut = ethers.parseUnits("60", 6);

// Approve YES tokens to Router
await yesToken.approve(router.target, yesIn);

// Sell YES
await router.sellYes(marketId, yesIn, minUsdcOut, signer.address, deadline);
```

### Buy NO

```typescript
// Buy NO requires a mintAmount parameter (used internally for split)
const usdcIn = ethers.parseUnits("100", 6);
const mintAmount = ethers.parseUnits("100", 6); // Amount to split
const minNoOut = ethers.parseUnits("90", 6);

await usdc.approve(router.target, usdcIn);
await router.buyNo(marketId, usdcIn, mintAmount, minNoOut, signer.address, deadline);
```

## Quote Before Trading

Always get a quote before executing:

```typescript
const expectedYes = await router.quoteBuyYes(marketId, usdcIn);
console.log("Expected YES:", ethers.formatUnits(expectedYes, 6));

// Set minOut with slippage tolerance
const minYesOut = expectedYes * 98n / 100n; // 2% slippage
```

---

**Next**: [Limit Orders](limit-orders.md) · [Smart Routing](smart-routing.md) · [Developer Trading Integration](../developers/trading-integration.md)
