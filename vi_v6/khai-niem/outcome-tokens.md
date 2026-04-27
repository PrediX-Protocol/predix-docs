# Outcome token (YES / NO)

Mỗi market có 2 ERC-20 token. Hiểu chúng = hiểu cơ chế cốt lõi của PrediX.

## Đặc điểm

- Cả 2 đều là **ERC-20 chuẩn**, 6 decimals (giống USDC).
- Mỗi market có cặp YES/NO riêng — token YES của market A không dùng được ở market B.
- Mint chỉ bởi Diamond contract, không ai khác (`onlyFactory`).

## Bất biến cốt lõi — $1 invariant

```
YES.totalSupply == NO.totalSupply == market.totalCollateral
```

Tổng supply YES = Tổng supply NO = USDC đang lock trong market. **Luôn luôn**.

```mermaid
flowchart TB
    USDC([100 USDC lock trong market])
    USDC --> Mint
    Mint{{Diamond mint atomic}}
    Mint --> YES([100 YES tokens])
    Mint --> NO([100 NO tokens])
    YES -.->|YES wins| RW([Redeem 100 USDC])
    NO -.->|NO wins| RW

    classDef in fill:#2563eb,stroke:#1d4ed8,color:#fff,stroke-width:2px
    classDef tok fill:#475569,stroke:#334155,color:#fff,stroke-width:1.5px
    classDef out fill:#16a34a,stroke:#15803d,color:#fff,stroke-width:2px
    class USDC in
    class YES,NO,Mint tok
    class RW out
```

Đảm bảo: **khi resolve, protocol luôn đủ USDC trả người giữ token đúng**. Không thể insolvent.

## Split — mint YES + NO từ USDC

```mermaid
flowchart LR
    U[Bạn] -->|deposit 100 USDC| D[Diamond]
    D -->|mint 100 YES| U
    D -->|mint 100 NO| U
```

- Atomic: cả 3 step trong 1 tx.
- Free phí protocol. Gas mặc định user trả; sponsor cover nếu đủ điều kiện chương trình (áp dụng cả 2 account types).

**Khi nào dùng**:
- Market-make: bán YES và NO riêng với giá > $0.50 mỗi token (giá trung bình > $1, ăn spread).
- Arbitrage khi YES + NO > $1 trên thị trường.

## Merge — burn YES + NO → USDC

```mermaid
flowchart LR
    U[Bạn] -->|burn 100 YES + 100 NO| D[Diamond]
    D -->|payout 100 USDC| U
```

Ngược split. Cần giữ đủ cả 2 với số lượng bằng nhau.

**Khi nào dùng**:
- Có cả YES và NO (do trade), muốn rút.
- Arbitrage khi YES + NO < $1.

## Tại sao YES + NO luôn ≈ $1

Vì split / merge là **arbitrage riskless**:

```mermaid
flowchart TD
    Check{YES + NO ≠ $1?}
    Check -->|>$1| Path1[Split 1 USDC<br/>→ 1 YES + 1 NO<br/>→ Sell cả 2 với tổng > $1<br/>= Profit]
    Check -->|<$1| Path2[Buy 1 YES + 1 NO < $1<br/>→ Merge → 1 USDC<br/>= Profit]
    Path1 -->|nhiều người làm| Eq[YES + NO bị kéo về ~$1]
    Path2 -->|nhiều người làm| Eq

    classDef arb fill:#475569,stroke:#334155,color:#fff,stroke-width:1.5px
    classDef eq fill:#16a34a,stroke:#15803d,color:#fff,stroke-width:2px
    class Path1,Path2 arb
    class Eq eq
```

Arbitrage tự động bởi AMM bots, không cần người vận hành. Chênh lệch tiny (vài bps) tồn tại do AMM curve và spread, nhưng không bao giờ lớn.

## Redeem — sau khi market resolve

- Market resolve, ví dụ outcome = YES.
- User giữ YES → call `redeem(marketId)` → mỗi 1 YES đổi 1 USDC (trừ redemption fee, default 0%).
- NO token → $0, không redeem được, không trade được nữa.

## Refund — khi market không resolve

Nếu oracle fail (down, dispute hung), admin enable **refund mode** qua timelock 48h:

- User burn cặp YES + NO → nhận USDC pro-rata.
- Burn theo `min(yesBalance, noBalance)` — chỉ refund được phần paired.

Chi tiết: [Resolution](resolution.md), [Redeem & refund](../huong-dan/redeem-va-claim.md).

## Composable với DeFi

Vì là ERC-20 standard, YES / NO có thể:

- Bỏ vào Uniswap pool khác (e.g. YES vs custom token).
- Dùng làm collateral lending (khi protocol lending list YES/NO).
- NFT mint condition (mint khi giữ YES của market resolve true).
- Vault strategy (e.g. auto-merge khi spread arbitrage).

Đây là điểm khác biệt lớn so với Polymarket (ERC-1155 không lending nào list).

## Encoding chi tiết

- Amount 1 USDC = `1_000_000` (6 decimals base unit).
- Amount 1 YES = `1_000_000` (cũng 6 decimals).
- Price $0.48 = `480_000` (fixed-point 6 decimals). Range hợp lệ: `10_000` đến `990_000` (0.01 đến 0.99).

Detail integration: [developers/router-integration.md](../developers/router-integration.md).
