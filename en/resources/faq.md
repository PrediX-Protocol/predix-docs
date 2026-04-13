---
description: Frequently asked questions about PrediX Protocol
---

# FAQ

## What is PrediX?

PrediX Protocol is a decentralized prediction market platform built on Uniswap v4 (Unichain L2). It combines an on-chain CLOB and AMM to provide the best trading experience for prediction markets.

## How do I start trading?

1. Connect your wallet to Unichain Sepolia
2. Get test USDC from the faucet
3. Approve USDC to the Router contract
4. Call `buyYes` or `buyNo` through the Router

See the [Quick Start](../getting-started/quick-start.md) guide for details.

## What happens when a market resolves?

The oracle reports the outcome, then anyone can call `resolveMarket`. Holders of the winning token redeem $1.00 per token. Losing tokens become worthless.

## Can I lose more than I invested?

**No.** Your maximum loss is the amount you paid for outcome tokens. There is no leverage, margin, or liquidation.

## How are prices determined?

Prices are set by supply and demand across two venues: the AMM pool and the CLOB order book. The Smart Router aggregates both sources. Prices naturally reflect the market's collective probability estimate.

## What is Split/Merge?

- **Split**: Deposit 1 USDC → receive 1 YES + 1 NO token
- **Merge**: Burn 1 YES + 1 NO → receive 1 USDC

This mechanism anchors token prices so that YES + NO ≈ $1.00.

## What are the fees?

AMM fees range from 0.5% (> 7 days until expiry) to 5% (< 24 hours). CLOB fees come from MINT/MERGE surplus. See [Fees](../concepts/fees.md) for details.

## Is PrediX audited?

Yes. 264 tests, 14 fuzz tests (10K runs each), 31 on-chain E2E tests, 3 independent audits with 22 bugs found and fixed, and 10/10 OWASP SC Top 10 2026 coverage. See [Audit Reports](../security/audit-reports.md).

## What chain is PrediX on?

Unichain Sepolia (testnet). Mainnet deployment on Unichain is planned for Q3 2026.

## How do I get test USDC?

Visit the PrediX testnet faucet or mint test USDC directly at address `0x12fd156C8b5F2901BA2781d97db84AaC56b2b911` on Unichain Sepolia.

---

**Next**: [Glossary](glossary.md) · [Quick Start](../getting-started/quick-start.md)
