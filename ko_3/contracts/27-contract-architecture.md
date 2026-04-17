---
description: 스마트 컨트랙트 아키텍처 및 배포 주소
---

# Contract Architecture

## 5개 핵심 모듈

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

### 왜 Diamond Proxy (EIP-2535)인가

* **24KB 제한 극복**: 복잡한 프로토콜 로직은 EVM 컨트랙트 크기 제한을 초과
* **세밀한 업그레이드**: 전체 재배포 없이 개별 함수 교체
* **공유 스토리지**: 모든 Facet이 동일한 스토리지 레이아웃을 공유
* **프로덕션 검증**: Aavegotchi, Beanstalk 등에서 사용

### Contract Addresses (Unichain Sepolia Testnet)

<table><thead><tr><th width="205">Contract</th><th>Address</th></tr></thead><tbody><tr><td>Diamond</td><td>0xF38a265E6e4F57D000a1CC08004da5B4A380B08A</td></tr><tr><td>Hook</td><td>0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0</td></tr><tr><td>Exchange</td><td>0xa202abCb2A358c0862B2dA76b553398339F2C638</td></tr><tr><td>Router</td><td>0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7</td></tr><tr><td>Oracle</td><td>0x699A8C74663b1C852E195b2ffa00D5965E992Cf3</td></tr><tr><td>USDC</td><td>0x12fd156C8b5F2901BA2781d97db84AaC56b2b911</td></tr><tr><td>Pool Manager</td><td>0x00B036B58a818B1BC34d502D3fE730Db729e62AC</td></tr></tbody></table>

### 체인 정보 <a href="#thong-tin-chuoi" id="thong-tin-chuoi"></a>

| 파라미터  | 값                            |
| ----- | ---------------------------- |
| 네트워크  | Unichain Sepolia (Testnet)   |
| Chain ID | `1301`                     |
| RPC   | `https://sepolia.unichain.org` |
| Explorer | `https://sepolia.uniscan.xyz` |
