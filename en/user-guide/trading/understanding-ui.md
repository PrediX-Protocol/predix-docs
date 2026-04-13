---
description: Understanding the PrediX trading interface
---

# Understanding the UI

## Main Layout

The trading page has 4 main areas:

```
┌──────────────────────┬─────────────────┐
│                      │  Your Position  │
│   Price Chart        │  YES: 65 shares │
│                      ├─────────────────┤
│                      │  Trading Panel  │
│                      │  Buy / Sell     │
│                      │  Market / Limit │
│                      │  [Amount]       │
│                      │  [Buy YES]      │
├──────────────────────┤                 │
│   Order Book         │  Split / Merge  │
│   Bids    Asks       │                 │
│   $0.35   $0.40      │  Market Info    │
│   $0.34   $0.41      │                 │
└──────────────────────┴─────────────────┘
```

## Your Position (top right)

Shows what you currently hold in this market:

```
Your Position
  YES    65 shares     ← you own 65 YES tokens
  NO     0 shares      ← you don't own any NO tokens
```

## Trading Panel (right)

### YES / NO Buttons
```
┌─────────┐ ┌─────────┐
│  YES    │ │   NO    │
│  38¢    │ │   62¢   │
└─────────┘ └─────────┘
```
- Green highlighted = selected
- Price shown = current market price

### Buy / Sell Toggle
- **Buy**: spend USDC to get shares
- **Sell**: sell your shares for USDC

### Market / Limit Toggle
- **Market**: buy/sell instantly at current price
- **Limit**: set your own price, wait for it

### Amount Input
```
Amount:              Balance: $17,927.54
┌──────────────────────────────────────┐
│ $ 50.00                         MAX │
└──────────────────────────────────────┘
  [$25]  [$50]  [$75]  [$100]
```
- Type amount or click preset buttons
- **MAX** = your entire balance
- **Balance** shown for reference

### Action Button
```
┌──────────────────────────────────────┐
│         Buy YES — $0.38              │
└──────────────────────────────────────┘
```
Shows what you're about to do and at what price.

## Price Chart (left)

Shows price history over time:
- Green line = price going up
- Red line = price going down
- Hover to see price at specific time

## Order Book (bottom left)

```
Price    Qty        Total
$0.40    61.58      543.73    ← Asks (sellers)
$0.39    69.68      482.15
$0.38    70.34      412.47

         $0.38   Spread: $0.03      ← Mid price

$0.35    24.67      24.67     ← Bids (buyers)
$0.34    31.87      56.54
$0.33    31.29      87.83
```

- **Red (top)** = Asks — people selling at these prices
- **Green (bottom)** = Bids — people buying at these prices
- **Spread** = gap between best bid and best ask

## Split / Merge Panel

```
┌──────────────────────────────┐
│  [Split]        [Merge]      │
│                              │
│  Split USDC into equal       │
│  YES + NO tokens             │
│                              │
│  ┌────────────────────┐      │
│  │ 100.00        USDC │      │
│  └────────────────────┘      │
│                              │
│  ┌────────────────────┐      │
│  │       Split         │      │
│  └────────────────────┘      │
└──────────────────────────────┘
```

- **Split**: $100 USDC → 100 YES + 100 NO tokens
- **Merge**: 100 YES + 100 NO → $100 USDC
- Useful for advanced strategies

## Market Info

```
Market Info
  Category:          crypto
  Total Collateral:  $9,936.67 USDC
  Status:            active
  Oracle:            0x699a8c...992cf3
```

- **Total Collateral**: how much USDC is locked in this market
- **Status**: active / ended / resolved
- **Oracle**: who determines the outcome

---

**Next**: [Cancel Orders](cancel-orders.md) · [Claim Winnings](../resolution/claim-winnings.md)
