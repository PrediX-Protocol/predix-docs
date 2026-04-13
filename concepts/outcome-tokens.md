---
description: YES and NO tokens — ERC-20 standard with full DeFi composability
---

# Outcome Tokens

## Overview

Each PrediX market creates two **ERC-20** outcome tokens:

| Token | Pays $1.00 if... | Pays $0.00 if... |
| ----- | ----------------- | ----------------- |
| **YES** | Event occurs | Event does not occur |
| **NO** | Event does not occur | Event occurs |

## Technical Specifications

| Property | Value |
| -------- | ----- |
| Standard | ERC-20 + ERC-2612 (Permit) |
| Decimals | 6 (matches USDC) |
| Mintable by | Diamond contract only (`onlyFactory` modifier) |
| Burnable by | Diamond contract only |
| Immutable fields | `factory`, `marketId`, `isYesToken` |

## ERC-2612 Permit

Outcome tokens support gasless approvals via the `permit` function. This allows users to approve and transfer in a single transaction using off-chain signatures.

```typescript
// Sign a permit off-chain
const deadline = Math.floor(Date.now() / 1000) + 3600;
const nonce = await yesToken.nonces(userAddress);
const signature = await signer.signTypedData(domain, types, { owner, spender, value, nonce, deadline });

// Execute permit on-chain (no prior approve needed)
await yesToken.permit(owner, spender, value, deadline, v, r, s);
```

## Core Invariant

```
YES.totalSupply == NO.totalSupply == market.totalCollateral
```

This invariant holds true across **all** operations: `splitPosition`, `mergePositions`, `groupSplit`, `groupMerge`, `redeemMarketTokens`, and `refund`.

## Address Generation

YES token addresses must be less than the USDC address (Uniswap v4 pool ordering requirement). PrediX uses **CREATE2 salt mining** to deterministically find compliant addresses.

> ⚠️ **Important**: Outcome tokens can ONLY be minted and burned by the Diamond contract. There is no public mint function.

---

**Next**: [Split & Merge](split-and-merge.md) · [Fees](fees.md) · [Trading Overview](../trading/overview.md)
