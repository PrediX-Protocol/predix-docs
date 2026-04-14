# Oracle Contract

PrediX uses a pluggable multi-oracle system. Each market specifies its own oracle adapter at creation time.

## IOracle Interface

```solidity
interface IOracle {
    function getResolution(bytes32 marketId) external view returns (bool resolved, bool outcome);
    function getResolvedAt(bytes32 marketId) external view returns (uint64);
}
```

Any contract implementing `IOracle` can serve as a market oracle, provided it's approved via `setApprovedOracle`.

## Available Adapters

### ManualOracleAdapter (Deployed)

Admin-resolved oracle for general-purpose markets.

```solidity
resolve(bytes32 marketId, bool outcome)  // ADMIN only
getResolution(bytes32 marketId) → (bool resolved, bool outcome)
getResolvedAt(bytes32 marketId) → uint64
```

**Address:** `0x699A8C74663b1C852E195b2ffa00D5965E992Cf3`

### ChainlinkAdapter (Planned)

Automatic price-based resolution using Chainlink price feeds.

- Market: "Will ETH be above $10K by Dec 31?"
- Adapter checks Chainlink ETH/USD feed after `endTime`
- Resolves automatically — no admin intervention

### UMACrossChainAdapter (Planned)

Event-based resolution via UMA's optimistic oracle, bridged to Unichain through Hyperlane.

## Creating a Custom Oracle

1. Implement the `IOracle` interface
2. Deploy your adapter contract on Unichain
3. Have an admin call `diamond.setApprovedOracle(yourAdapter, true)`
4. Pass your adapter address when creating markets

```solidity
contract MyCustomOracle is IOracle {
    mapping(bytes32 => bool) public resolved;
    mapping(bytes32 => bool) public outcomes;
    mapping(bytes32 => uint64) public resolvedAt;

    function resolve(bytes32 marketId, bool outcome) external {
        // Your resolution logic
        resolved[marketId] = true;
        outcomes[marketId] = outcome;
        resolvedAt[marketId] = uint64(block.timestamp);
    }

    function getResolution(bytes32 marketId) external view returns (bool, bool) {
        return (resolved[marketId], outcomes[marketId]);
    }

    function getResolvedAt(bytes32 marketId) external view returns (uint64) {
        return resolvedAt[marketId];
    }
}
```

## Oracle Approval

Only approved oracles can be assigned to markets:

```solidity
// ADMIN_ROLE
diamond.setApprovedOracle(oracleAddress, true);   // approve
diamond.setApprovedOracle(oracleAddress, false);  // revoke
```

## Next Steps

- [Security](security.md) — emergency resolution fallbacks
- [Resolution](../concepts/resolution.md) — full resolution lifecycle
- [Developer: Resolve & Redeem](../developers/resolve-redeem.md) — code examples
