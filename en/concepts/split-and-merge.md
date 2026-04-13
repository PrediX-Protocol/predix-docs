---
description: Split USDC into outcome tokens, or merge them back
---

# Split & Merge

Split and Merge are the foundational mechanisms that anchor outcome token prices to real value.

## Split

Deposit USDC collateral to receive equal amounts of YES and NO tokens:

```
1 USDC → 1 YES + 1 NO
```

The collateral is locked in the Diamond contract. Both token supplies increase equally.

## Merge

Burn equal amounts of YES and NO tokens to withdraw USDC:

```
1 YES + 1 NO → 1 USDC
```

The collateral is released from the Diamond. Both token supplies decrease equally.

## Why This Matters

Split/Merge creates the **price anchor** for the entire market:

```
If YES = $0.70 and NO = $0.40:
  YES + NO = $1.10 > $1.00
  → Arbitrageur: split $1 → sell YES for $0.70 + sell NO for $0.40 = $1.10
  → Profit: $0.10
  → Selling pressure pushes prices back to sum ≈ $1.00
```

## Code Example

```typescript
import { ethers } from "ethers";

const diamond = new ethers.Contract(DIAMOND_ADDRESS, DIAMOND_ABI, signer);
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, signer);

const marketId = "0x...";
const amount = ethers.parseUnits("100", 6); // 100 USDC

// Step 1: Approve USDC to Diamond
await usdc.approve(DIAMOND_ADDRESS, amount);

// Step 2: Split — receive 100 YES + 100 NO
await diamond.splitPosition(marketId, amount);

// Step 3: Merge — burn 100 YES + 100 NO, receive 100 USDC
await diamond.mergePositions(marketId, amount);
```

## Safety Caps

Split operations are subject to safety caps:

| Cap | Description |
| --- | ----------- |
| **TVL Cap** | Maximum total USDC locked in the system |
| **Per-Trade Cap** | Maximum USDC per single split operation |
| **Per-Market Cap** | Maximum USDC per individual market |

> ⚠️ **Note**: You must hold equal amounts of YES and NO tokens to merge. The merge amount cannot exceed your balance of either token.

---

**Next**: [Resolution](resolution.md) · [Multi-Outcome Markets](multi-outcome-markets.md) · [Trading Overview](../trading/overview.md)
