# Smart Routing

The Router aggregates liquidity from both CLOB and AMM for best execution.

## How It Works

1. **Check CLOB:** Router scans the Exchange order book for matching limit orders
2. **Fill from CLOB:** Execute against available limit orders at their prices
3. **Route overflow to AMM:** Any unfilled amount goes to the Uniswap v4 pool

### Example

Buy 100 YES with USDC. CLOB has 60 YES available at $0.55–$0.60. AMM price is $0.62.

| Source | Amount | Price | Cost |
|--------|--------|-------|------|
| CLOB | 60 YES | $0.55–$0.60 avg | ~$34.50 |
| AMM | 40 YES | $0.62–$0.65 | ~$25.40 |
| **Total** | **100 YES** | **$0.599 avg** | **~$59.90** |

Without the Router, buying all 100 from AMM alone would cost more due to price impact.

## Quote Functions

Get expected output before committing:

```typescript
const router = new ethers.Contract(ROUTER, [
  "function quoteBuyYes(bytes32,uint256) view returns (uint256)",
  "function quoteSellYes(bytes32,uint256) view returns (uint256)",
  "function quoteBuyNo(bytes32,uint256) view returns (uint256)",
  "function quoteSellNo(bytes32,uint256) view returns (uint256)"
], provider);

// How many YES tokens for 50 USDC?
const yesOut = await router.quoteBuyYes(marketId, ethers.parseUnits("50", 6));
console.log(`Expected: ${ethers.formatUnits(yesOut, 6)} YES tokens`);
```

## When CLOB is Better vs AMM

| Scenario | Better Venue | Why |
|----------|-------------|-----|
| Large order, thin book | AMM | Consistent pricing, no partial fills |
| Precise price target | CLOB | Exact price via limit order |
| Small order, deep book | CLOB | Better price, no AMM fee |
| Near expiry (high AMM fee) | CLOB | AMM fee reaches 5% |

## Next Steps

- [Matching Engine](matching-engine.md) — CLOB matching algorithm
- [Fees](../concepts/fees.md) — how fees affect routing decisions
- [Contract: Router](../contracts/router.md) — technical implementation
