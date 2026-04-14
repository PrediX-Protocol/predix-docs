# Liquidity Overview

PrediX has two types of liquidity provision: AMM LP (passive) and CLOB market making (active).

## AMM Liquidity (Uniswap v4)

Provide liquidity to YES/USDC pools on Uniswap v4 and earn dynamic swap fees.

### Fee Earnings

| Time to Expiry | Fee Rate | Your Share |
|----------------|----------|------------|
| > 7 days | 0.5% | LP pro-rata |
| 3–7 days | 1.0% | LP pro-rata |
| 1–3 days | 2.0% | LP pro-rata |
| < 24 hours | 5.0% | LP pro-rata |

Fees increase near expiry, compensating LPs for higher information asymmetry risk. See [Fees](../concepts/fees.md) for complete schedule.

### How to Provide AMM Liquidity

#### Step 1: Get YES Tokens

Split USDC into YES + NO tokens via the Diamond contract. See [Split & Merge](../concepts/split-and-merge.md).

```typescript
// Split $1000 USDC → 1000 YES + 1000 NO
await usdc.approve(DIAMOND, ethers.parseUnits("1000", 6));
await diamond.splitPosition(marketId, ethers.parseUnits("1000", 6));
```

#### Step 2: Approve Tokens to Position Manager

```typescript
const yesToken = new ethers.Contract(market.yesToken, ["function approve(address,uint256)"], signer);
await yesToken.approve(POSITION_MANAGER, ethers.MaxUint256);
await usdc.approve(POSITION_MANAGER, ethers.MaxUint256);
```

#### Step 3: Mint LP Position

Use Uniswap v4's PositionManager to add concentrated or full-range liquidity.

### Concentrated vs Full-Range

| Strategy | Pros | Cons |
|----------|------|------|
| **Concentrated** ($0.30–$0.70) | Higher fee yield, capital efficient | Must rebalance, more IL risk |
| **Full-range** ($0.01–$0.99) | No rebalancing needed | Lower yield, capital inefficient |

### Risks

- **Impermanent loss:** If price moves significantly, your position may lose value relative to holding
- **Toxic flow near expiry:** Informed traders trade aggressively before resolution, LPs absorb losses
- **Resolution risk:** If you don't withdraw before resolution, winning side tokens are redeemed automatically

> ⚠️ **Remove LP positions before the market expires.** After expiry, the AMM pool may become illiquid and difficult to exit.

### Best Practices

1. LP in markets with > 7 days to expiry (lowest fees, lowest toxic flow)
2. Use concentrated ranges around current probability
3. Monitor position and withdraw 24–48 hours before expiry
4. Rebalance when price moves significantly outside your range

## Next Steps

- [Market Making](market-making.md) — CLOB liquidity provision
- [Fees](../concepts/fees.md) — detailed fee schedule
- [Split & Merge](../concepts/split-and-merge.md) — getting YES tokens for LP
