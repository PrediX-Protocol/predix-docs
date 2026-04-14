# Oracle Contract

PrediX dùng hệ thống multi-oracle plug-in. Mỗi market chỉ định oracle adapter riêng khi tạo.

## Interface IOracle

```solidity
interface IOracle {
    function getResolution(bytes32 marketId) external view returns (bool resolved, bool outcome);
    function getResolvedAt(bytes32 marketId) external view returns (uint64);
}
```

Bất kỳ contract nào cài đặt `IOracle` đều có thể làm oracle cho market, miễn được approve qua `setApprovedOracle`.

## Adapter có sẵn

### ManualOracleAdapter (Đã deploy)

Oracle do admin phân xử, dùng cho market tổng quát.

```solidity
resolve(bytes32 marketId, bool outcome)  // Chỉ ADMIN
getResolution(bytes32 marketId) → (bool resolved, bool outcome)
getResolvedAt(bytes32 marketId) → uint64
```

**Địa chỉ:** `0x699A8C74663b1C852E195b2ffa00D5965E992Cf3`

### ChainlinkAdapter (Kế hoạch)

Phân xử tự động dựa trên giá, dùng Chainlink price feed.

- Market: "ETH có vượt $10K trước 31/12?"
- Adapter check Chainlink ETH/USD feed sau `endTime`
- Tự resolve — không cần admin can thiệp

### UMACrossChainAdapter (Kế hoạch)

Phân xử theo sự kiện qua optimistic oracle của UMA, bridge sang Unichain qua Hyperlane.

## Tạo oracle tuỳ biến

1. Cài đặt interface `IOracle`
2. Deploy adapter lên Unichain
3. Admin gọi `diamond.setApprovedOracle(yourAdapter, true)`
4. Truyền địa chỉ adapter khi tạo market

```solidity
contract MyCustomOracle is IOracle {
    mapping(bytes32 => bool) public resolved;
    mapping(bytes32 => bool) public outcomes;
    mapping(bytes32 => uint64) public resolvedAt;

    function resolve(bytes32 marketId, bool outcome) external {
        // Logic phân xử của bạn
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

## Approve oracle

Chỉ oracle đã approve mới được gán cho market:

```solidity
// ADMIN_ROLE
diamond.setApprovedOracle(oracleAddress, true);   // approve
diamond.setApprovedOracle(oracleAddress, false);  // revoke
```

## Tiếp theo

- [Bảo mật](security.md) — fallback phân xử khẩn cấp
- [Phân xử](../concepts/resolution.md) — vòng đời phân xử đầy đủ
- [Developer: Resolve & Redeem](../developers/resolve-redeem.md) — ví dụ code
