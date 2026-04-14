# Outcome Tokens

YES and NO tokens are standard **ERC-20** tokens with **ERC-2612 Permit** support, deployed per market by the Diamond contract.

## Specifications

| Property | Value |
|----------|-------|
| Standard | ERC-20 + ERC-2612 Permit |
| Decimals | 6 (matches USDC) |
| Minting | Diamond contract only (`onlyFactory`) |
| Burning | Diamond contract only (on merge/redeem) |
| Price range | $0.01 – $0.99 |
| Transferable | Yes — standard ERC-20 transfers |

## Supply Invariant

```
YES supply = NO supply = totalCollateral (in USDC)
```

Every YES token in existence has a matching NO token. Both are backed 1:1 by USDC locked in the Diamond contract. This invariant is enforced by the split/merge mechanism — see [Split & Merge](split-and-merge.md).

## DeFi Composability

Because outcome tokens are ERC-20:

- **LP on Uniswap v4** — earn swap fees while holding a prediction position
- **Gasless approvals** — use `permit()` instead of `approve()` for better UX
- **Transfer freely** — send to any wallet, use in multisigs, integrate into vaults
- **Future:** collateral on lending protocols, structured products

> ⚠️ Only the Diamond contract can mint/burn outcome tokens. Direct transfers to the Diamond address will result in loss of funds.

## Next Steps

- [Split & Merge](split-and-merge.md) — how tokens are created and destroyed
- [Trading Overview](../trading/overview.md) — how to buy and sell tokens
- [Contract: Diamond](../contracts/diamond.md) — minting architecture
