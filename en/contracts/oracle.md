---
description: Pluggable oracle system — Manual, Chainlink, and custom adapters
---

# Oracle System

PrediX uses a pluggable oracle architecture. Every oracle implements the same interface:

```solidity
interface IOracle {
    function getResolution(bytes32 marketId)
        external view returns (bool resolved, bool outcome);
}
```

## ManualOracleAdapter

Admin-controlled resolution for subjective events (politics, sports, etc.).

```solidity
resolve(bytes32 marketId, bool outcome)     // ADMIN only
getResolution(bytes32 marketId) → (bool resolved, bool outcome)
getResolvedAt(bytes32 marketId) → uint64
```

## ChainlinkAdapter

Automatic resolution based on Chainlink price feeds. Example: "BTC > $100K?"

**Safety checks**: stale feed detection (24h), L2 sequencer uptime, round completeness.

## Creating a Custom Oracle

1. Implement the `IOracle` interface
2. Deploy your adapter contract
3. Admin approves: `diamond.setApprovedOracle(adapterAddress, true)`
4. Use when creating markets: `createMarket(..., adapterAddress)`

```solidity
contract MyCustomOracle is IOracle {
    mapping(bytes32 => bool) public resolved;
    mapping(bytes32 => bool) public outcomes;

    function getResolution(bytes32 marketId)
        external view returns (bool, bool)
    {
        return (resolved[marketId], outcomes[marketId]);
    }

    function resolve(bytes32 marketId, bool outcome) external {
        // Your resolution logic here
        resolved[marketId] = true;
        outcomes[marketId] = outcome;
    }
}
```

> ⚠️ **Oracle approval**: Only admin-approved oracle addresses can be used when creating markets.

---

**Next**: [Access Control](access-control.md) · [Safety](safety.md) · [Resolution Concept](../concepts/resolution.md)
