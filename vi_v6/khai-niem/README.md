# Khái niệm

Hiểu PrediX hoạt động ra sao. Đọc theo thứ tự nếu mới, hoặc nhảy tới mục cần.

```mermaid
%%{init: {"theme":"base","themeVariables":{"primaryColor":"#1e293b","primaryTextColor":"#f1f5f9","primaryBorderColor":"#475569","lineColor":"#94a3b8","background":"transparent","mainBkg":"#334155","tertiaryColor":"#475569"}}}%%
mindmap
  root((PrediX))
    Prediction market
      Giá = xác suất
      Token YES NO
    Outcome token
      ERC-20 composable
      Split / merge
      Bất biến $1
    Liquidity
      CLOB on-chain
      Uniswap v4 AMM
      Router hybrid
    Resolution
      Manual oracle
      Chainlink
      UMA
      Refund mode
    Fee
      Dynamic AMM
      CLOB taker
      Redemption
      Distribution adaptive 4-phase
```

## Đọc theo thứ tự

- [Prediction market](prediction-market.md) — Giá = xác suất
- [Outcome token](outcome-tokens.md) — YES/NO, split/merge, bất biến $1
- [CLOB + AMM hybrid](clob-va-amm.md) — Order book on-chain + Uniswap v4 pool
- [Resolution & oracle](resolution.md) — Ai quyết định kết quả
- [Cấu trúc fee](phi.md) — AMM, CLOB, redemption, distribution

Cần tutorial từng bước (click chỗ nào, nhập gì) → [Hướng dẫn](../huong-dan/README.md).

Cần tech detail (smart contract, event, storage) → [Giao thức](../giao-thuc/README.md).
