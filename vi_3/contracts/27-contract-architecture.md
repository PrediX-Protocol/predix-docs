---
description: Kiến trúc hợp đồng thông minh và địa chỉ triển khai
---

# Contract Architecture

## 5 Module Chính

```
User → Router (Smart Router)
         ├── Exchange (CLOB)
         └── Hook (AMM via Uniswap v4)
              └── Pool Manager (Uniswap v4)

Diamond Proxy (EIP-2535)
├── CutFacet
├── LoupeFacet
├── AccessControlFacet
├── OwnableFacet
├── PausableFacet
└── MarketFacet

Oracle (IOracle interface)
├── ManualOracleAdapter
├── ChainlinkAdapter
└── UMACrossChainAdapter (Phase 3)
```

### Tại sao sử dụng Diamond Proxy (EIP-2535)

* **Vượt qua giới hạn 24KB**: Logic giao thức phức tạp vượt quá giới hạn kích thước hợp đồng EVM
* **Nâng cấp chi tiết**: Thay thế các hàm riêng lẻ mà không cần triển khai lại toàn bộ
* **Lưu trữ chia sẻ**: Tất cả Facet chia sẻ cùng một bố cục lưu trữ
* **Đã được kiểm chứng trong sản xuất**: Được sử dụng bởi Aavegotchi, Beanstalk và các dự án khác

### Contract Addresses (Unichain Sepolia Testnet)

<table><thead><tr><th width="205">Contract</th><th>Address</th></tr></thead><tbody><tr><td>Diamond</td><td>0xF38a265E6e4F57D000a1CC08004da5B4A380B08A</td></tr><tr><td>Hook</td><td>0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0</td></tr><tr><td>Exchange</td><td>0xa202abCb2A358c0862B2dA76b553398339F2C638</td></tr><tr><td>Router</td><td>0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7</td></tr><tr><td>Oracle</td><td>0x699A8C74663b1C852E195b2ffa00D5965E992Cf3</td></tr><tr><td>USDC</td><td>0x12fd156C8b5F2901BA2781d97db84AaC56b2b911</td></tr><tr><td>Pool Manager</td><td>0x00B036B58a818B1BC34d502D3fE730Db729e62AC</td></tr></tbody></table>

### Thông tin chuỗi <a href="#thong-tin-chuoi" id="thong-tin-chuoi"></a>

| Tham số        | Giá trị                        |
| -------------- | ------------------------------ |
| Mạng           | Unichain Sepolia (Testnet)     |
| Chain ID       | `1301`                         |
| RPC            | `https://sepolia.unichain.org` |
| Trình khám phá | `https://sepolia.uniscan.xyz`  |
