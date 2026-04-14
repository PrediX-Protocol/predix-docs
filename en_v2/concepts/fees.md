# Fees

PrediX uses a hybrid fee model across AMM and CLOB venues.

## AMM Dynamic Fees

Fees increase as the market approaches expiry, compensating LPs for higher adverse selection risk.

| Time to Expiry | Fee Rate | Rationale |
|----------------|----------|-----------|
| > 7 days | 0.5% | Low fee encourages early trading |
| 3–7 days | 1.0% | Moderate fee, information value rising |
| 1–3 days | 2.0% | Higher risk for LPs |
| < 24 hours | 5.0% | Maximum protection near resolution |

## CLOB Fees

| Fee Type | Rate | Details |
|----------|------|---------|
| Taker fee | 0%–1% | Starts at 0%, increases over time |
| MINT surplus | Variable | When BUY_YES + BUY_NO sum > $1, surplus is protocol revenue |
| MERGE surplus | Variable | When SELL_YES + SELL_NO sum < $1, surplus is protocol revenue |

**Fee formula (symmetric):** `fee = feeRate × min(price, 1 − price) × quantity`

## Market Creation Fee

A configurable flat fee in USDC ($10–$50) per market, preventing spam and generating protocol revenue.

## Fee Example

Buy 100 YES tokens at $0.65 via AMM, market expires in 5 days:

```
Fee rate: 1.0% (3–7 days bucket)
Fee: 100 × $0.65 × 1.0% = $0.65
Total cost: $65.00 + $0.65 = $65.65
```

## Next Steps

- [Trading Overview](../trading/overview.md) — how trades are routed
- [Liquidity Overview](../liquidity/overview.md) — how LPs earn fees
- [Smart Routing](../trading/smart-routing.md) — fee impact on routing
