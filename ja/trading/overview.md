---
description: Two trading venues — CLOB and AMM — unified by the Smart Router
---

# Trading Overview

PrediX offers two trading venues unified by the Smart Router:

```
User → PrediXRouter (Smart Router)
          ├── PrediXExchange (CLOB) — limit orders, zero slippage
          └── Uniswap v4 AMM — always-on liquidity
```

## Market Orders vs Limit Orders

| Type | Via | Best for |
| ---- | --- | -------- |
| **Market Order** | Router (`buyYes`, `sellYes`, `buyNo`, `sellNo`) | Instant execution, best available price |
| **Limit Order** | Exchange (`placeOrder`) | Specific price, patient traders |

## Trading Flow

1. **Approve** USDC (or tokens) to the contract
2. **Quote** the expected output using `quoteBuyYes` / `quoteSellYes` etc.
3. **Execute** the trade with slippage protection (`minOut` parameter)
4. **Receive** outcome tokens (or USDC)

> ⚠️ **Always set a deadline** (typically 5 minutes from now) and **minOut** for slippage protection.

---

**Next**: [Market Orders](market-orders.md) · [Limit Orders](limit-orders.md) · [Smart Routing](smart-routing.md)
