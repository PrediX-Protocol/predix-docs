---
description: Fee structure for AMM, CLOB, and market creation
---

# Fees

## AMM Dynamic Fees

Swap fees on the Uniswap v4 AMM pool increase as the market approaches expiry. This protects liquidity providers from informed ("toxic") traders near resolution.

| Time Remaining | Fee   | Rationale |
| -------------- | ----- | --------- |
| > 7 days       | 0.5%  | Normal trading, encourage volume |
| 3–7 days       | 1.0%  | Begin caution |
| 1–3 days       | 2.0%  | Outcome becoming clearer |
| < 24 hours     | 5.0%  | High risk for LPs, deter toxic flow |

## CLOB Fees

The CLOB charges fees through the MINT and MERGE surplus mechanism:

### MINT Surplus

When BUY_YES and BUY_NO orders match with sum ≥ $1.00:

```
Fee = (takerPrice + makerPrice - $1.00) × amount

Example:
  BUY_YES @ $0.60 + BUY_NO @ $0.50 = $1.10
  Surplus = $0.10 per token → protocol treasury
```

### MERGE Surplus

When SELL_YES and SELL_NO orders match with sum ≤ $1.00:

```
Fee = ($1.00 - takerPrice - makerPrice) × amount

Example:
  SELL_YES @ $0.40 + SELL_NO @ $0.50 = $0.90
  Surplus = $0.10 per token → protocol treasury
```

## Market Creation Fee

Configurable per market. Set to **0** during beta period.

## Fee Summary

| Source | Fee | Recipient |
| ------ | --- | --------- |
| AMM swap | 0.5%–5% (dynamic) | LP providers |
| CLOB MINT surplus | Variable | Protocol treasury |
| CLOB MERGE surplus | Variable | Protocol treasury |
| Market creation | Configurable | Protocol treasury |

---

**Next**: [Trading Overview](../trading/overview.md) · [Providing Liquidity](../liquidity/overview.md)
