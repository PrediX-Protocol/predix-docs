---
description: How the Router aggregates CLOB and AMM liquidity
---

# Smart Routing

The PrediXRouter automatically finds the best execution path across both CLOB and AMM.

## How It Works

```
router.buyYes($100):
  Step 1: Check CLOB for SELL_YES orders with better price than AMM
  Step 2: Fill CLOB orders first (best price, zero slippage)
  Step 3: Route remaining amount through Uniswap v4 AMM
  Step 4: User receives total YES tokens from both sources
```

### Example

```
Buy 100 YES tokens:
  CLOB: 60 YES available @ $0.62 → cost: $37.20
  AMM:  40 YES @ ~$0.65 avg      → cost: $26.00
  Total: 100 YES for $63.20

Without Smart Router (AMM only):
  100 YES @ ~$0.66 avg → cost: $66.00
  
  Savings: $2.80 (4.2% better execution)
```

## Quote Functions

Always quote before trading:

```typescript
const router = new ethers.Contract(ROUTER_ADDRESS, ROUTER_ABI, provider);

const yesOut = await router.quoteBuyYes(marketId, usdcIn);
const usdcOut = await router.quoteSellYes(marketId, yesIn);
const noOut = await router.quoteBuyNo(marketId, usdcIn);
const usdcFromNo = await router.quoteSellNo(marketId, noIn);
```

## When CLOB is Better vs AMM

| Condition | Better venue |
| --------- | ------------ |
| Deep order book, tight spread | CLOB |
| No orders at desired price | AMM |
| Large orders | Split across both |
| Near market expiry (high AMM fees) | CLOB |

---

**Next**: [Matching Engine](matching-engine.md) · [Virtual NO Pricing](virtual-no-pricing.md)
