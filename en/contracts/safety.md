---
description: Safety caps, emergency controls, reentrancy guards, and anti-sandwich
---

# Safety Mechanisms

## Safety Caps

| Cap | Description | Configurable by |
| --- | ----------- | --------------- |
| TVL Cap | Max total USDC in the system | ADMIN |
| Per-Trade Cap | Max USDC per split/trade | ADMIN |
| Per-Market Cap | Max USDC per market | ADMIN |

Caps start conservative and can be increased as the protocol matures.

## Emergency Controls

| Mechanism | Role | Description |
| --------- | ---- | ----------- |
| Pause Module | PAUSER | Freeze specific market module |
| Pause All | PAUSER | Freeze entire system |
| Emergency Resolve | OPERATOR | Resolve after 7-day delay (oracle failure) |
| Refund Mode | OPERATOR | Proportional refund for all holders |

## Reentrancy Guards

Three independent guards protect different contract boundaries:

1. **Diamond storage guard**: Protects MarketFacet functions
2. **Exchange/Router transient storage**: Protects CLOB and routing
3. **Hook state guard**: Protects Uniswap v4 hook callbacks

20 functions are protected with `nonReentrant` modifiers.

## Anti-Sandwich Protection

Built into the Hook at protocol level. Detects same-user opposite-direction swaps in the same block.

Additional layer: Unichain provides sequencer-level revert protection.

## Invariants

```
1. YES.totalSupply == NO.totalSupply == totalCollateral (always)
2. Split(N) + Merge(N) = identity (zero value leak)
3. Total redeemed ≤ total deposited
4. Exchange balance ≥ total resting order deposits
5. Refund mode blocks ALL trading (split, merge, CLOB, AMM)
```

---

**Next**: [Security Overview](../security/overview.md) · [Access Control](access-control.md)
