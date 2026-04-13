---
description: Two ways to provide liquidity — AMM and CLOB
---

# Liquidity Overview

## Two Types of Liquidity

| Type | Via | Earns | Risk |
| ---- | --- | ----- | ---- |
| **AMM LP** | Uniswap v4 pool (YES/USDC) | Dynamic swap fees (0.5%–5%) | Impermanent loss |
| **CLOB Orders** | PrediXExchange limit orders | Spread capture | Inventory risk |

## AMM LP Fees

Fees are dynamic and increase near market expiry:

| Time Remaining | Fee |
| -------------- | --- |
| > 7 days | 0.5% |
| 3–7 days | 1.0% |
| 1–3 days | 2.0% |
| < 24 hours | 5.0% |

## Risks

- **Impermanent loss**: As the outcome becomes clearer, one token trends toward $1 and the other toward $0
- **Toxic flow**: Near expiry, informed traders know the outcome before resolution — high fees help mitigate this
- **Resolution risk**: LP positions in losing tokens become worthless

## Best Practices

1. Provide concentrated liquidity around the current price range
2. Monitor time-to-expiry — consider removing LP 24–48 hours before market ends
3. Use CLOB market-making for tighter spread control
4. Diversify across multiple markets

> ⚠️ **Critical**: Remove LP positions before market resolution. After resolution, the losing token becomes worthless.

---

**Next**: [Providing Liquidity](providing-liquidity.md) · [Market Making](market-making.md) · [Fees](../concepts/fees.md)
