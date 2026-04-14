# Multi-Outcome Markets

Categories group related binary markets for events with more than two possible outcomes.

## How It Works

**Example:** "Who will win the 2028 US Presidential Election?"

Instead of one market, create a **category** with one binary market per candidate:

| Market | Question |
|--------|----------|
| Market A | "Will Candidate A win?" |
| Market B | "Will Candidate B win?" |
| Market C | "Will Candidate C win?" |

Each market has its own YES/NO tokens. The category ensures exactly one outcome wins.

## Group Operations

### groupSplit

Deposit USDC once to receive YES tokens for **every** market in the category.

```
$100 USDC → 100 YES_A + 100 YES_B + 100 YES_C + ... (one per market)
```

### groupMerge

Return one complete set of YES tokens (one from each market) to receive USDC.

```
100 YES_A + 100 YES_B + 100 YES_C → $100 USDC
```

## Resolution

```typescript
// ADMIN_ROLE resolves the entire category
// winningOutcomeIndex = index of the winning market (0-based)
await diamond.resolveCategory(categoryId, 0); // Candidate A wins
```

## Next Steps

- [Split & Merge](split-and-merge.md) — single-market split/merge
- [Resolution](resolution.md) — resolution mechanics
- [Developer: Create Market](../developers/create-market.md) — creating categories
