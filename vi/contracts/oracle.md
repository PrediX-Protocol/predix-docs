---
description: Hệ thống oracle dạng cắm được — Manual, Chainlink và bộ điều hợp tùy chỉnh
---

# Hệ thống Oracle

PrediX sử dụng kiến trúc oracle dạng cắm được. Mọi oracle đều triển khai cùng một giao diện:

```solidity
interface IOracle {
    function getResolution(bytes32 marketId)
        external view returns (bool resolved, bool outcome);
}
```

## ManualOracleAdapter

Xác nhận kết quả do quản trị viên kiểm soát cho các sự kiện chủ quan (chính trị, thể thao, v.v.).

```solidity
resolve(bytes32 marketId, bool outcome)     // Chỉ quản trị viên
getResolution(bytes32 marketId) → (bool resolved, bool outcome)
getResolvedAt(bytes32 marketId) → uint64
```

## ChainlinkAdapter

Xác nhận kết quả tự động dựa trên nguồn giá Chainlink. Ví dụ: "BTC > $100K?"

**Kiểm tra an toàn**: phát hiện nguồn cấp cũ (24 giờ), thời gian hoạt động bộ tuần tự L2, tính hoàn chỉnh của vòng.

## Tạo Oracle tùy chỉnh

1. Triển khai giao diện `IOracle`
2. Triển khai hợp đồng bộ điều hợp
3. Quản trị viên phê duyệt: `diamond.setApprovedOracle(adapterAddress, true)`
4. Sử dụng khi tạo thị trường: `createMarket(..., adapterAddress)`

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
        // Logic xác nhận kết quả của bạn ở đây
        resolved[marketId] = true;
        outcomes[marketId] = outcome;
    }
}
```

> ⚠️ **Phê duyệt Oracle**: Chỉ các địa chỉ Oracle được quản trị viên phê duyệt mới có thể sử dụng khi tạo thị trường.

---

**Tiếp theo**: [Kiểm soát truy cập](access-control.md) · [An toàn](safety.md) · [Khái niệm xác nhận kết quả](../concepts/resolution.md)
