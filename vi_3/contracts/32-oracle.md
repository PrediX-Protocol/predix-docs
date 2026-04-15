# 32. Oracle

### IOracle Interface

```
resolve(marketId, outcome) — ADMIN
getResolution(marketId) → (resolved, outcome)
getResolvedAt(marketId) → uint64
```

### ManualOracleAdapter

Admin resolve thủ công. Dùng cho sự kiện mà không có price feed tự động.

### ChainlinkAdapter

Tự động resolve price markets dựa trên Chainlink feeds. VD: “BTC > $200K vào T12/2026” → check Chainlink BTC/USD.

### Custom Oracle Adapter

Implement IOracle interface để tạo adapter tuỳ chỉnh. Phải được approved qua setApprovedOracle().
