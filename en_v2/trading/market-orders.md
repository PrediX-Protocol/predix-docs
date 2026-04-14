# Market Orders

Market orders execute immediately at the best available price across CLOB and AMM.

## Functions

All market order functions are on the **Router** (`0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7`).

| Function | Action | Input | Output |
|----------|--------|-------|--------|
| `buyYes` | Buy YES tokens with USDC | `usdcIn` | `yesOut` |
| `sellYes` | Sell YES tokens for USDC | `yesIn` | `usdcOut` |
| `buyNo` | Buy NO tokens with USDC | `usdcIn, mintAmount` | `noOut` |
| `sellNo` | Sell NO tokens for USDC | `noIn` | `usdcOut` |

### Signatures

```solidity
buyYes(bytes32 marketId, uint256 usdcIn, uint256 minYesOut, address recipient, uint256 deadline) → uint256 yesOut
sellYes(bytes32 marketId, uint256 yesIn, uint256 minUsdcOut, address recipient, uint256 deadline) → uint256 usdcOut
buyNo(bytes32 marketId, uint256 usdcIn, uint256 mintAmount, uint256 minNoOut, address recipient, uint256 deadline) → uint256 noOut
sellNo(bytes32 marketId, uint256 noIn, uint256 minUsdcOut, address recipient, uint256 deadline) → uint256 usdcOut
```

## Code Examples

### Buy YES

```typescript
import { ethers } from "ethers";

const ROUTER = "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7";
const USDC = "0x12fd156C8b5F2901BA2781d97db84AaC56b2b911";

const usdc = new ethers.Contract(USDC, ["function approve(address,uint256)"], signer);
const router = new ethers.Contract(ROUTER, [
  "function buyYes(bytes32,uint256,uint256,address,uint256) returns (uint256)",
  "function sellYes(bytes32,uint256,uint256,address,uint256) returns (uint256)"
], signer);

// Approve USDC
await usdc.approve(ROUTER, ethers.parseUnits("50", 6));

// Buy 50 USDC worth of YES tokens, accept 2% slippage
const usdcIn = ethers.parseUnits("50", 6);
const minYesOut = ethers.parseUnits("45", 6); // ~10% slippage tolerance
const deadline = Math.floor(Date.now() / 1000) + 300; // 5 minutes

const tx = await router.buyYes(marketId, usdcIn, minYesOut, myAddress, deadline);
const receipt = await tx.wait();
```

### Sell YES

```typescript
// Approve YES tokens to Router first
const yesToken = new ethers.Contract(yesTokenAddress, ["function approve(address,uint256)"], signer);
await yesToken.approve(ROUTER, ethers.parseUnits("50", 6));

const tx = await router.sellYes(
  marketId,
  ethers.parseUnits("50", 6),  // sell 50 YES
  ethers.parseUnits("25", 6),  // min 25 USDC out
  myAddress,
  deadline
);
```

## Slippage Protection

- **`minOut`** — minimum tokens/USDC you'll accept. Transaction reverts if execution is worse.
- **`deadline`** — UNIX timestamp. Transaction reverts if not mined by this time.

> ⚠️ Always set `minOut` > 0 and a reasonable deadline. Without slippage protection, you may receive significantly less than expected.

## Next Steps

- [Limit Orders](limit-orders.md) — place orders at exact prices
- [Smart Routing](smart-routing.md) — how Router splits across venues
- [Developer: Trading Integration](../developers/trading-integration.md) — full integration guide
