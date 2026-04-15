# 27. Contract Architecture

## 5 Module Chính
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

## Tại Sao Diamond Proxy?
48 source files qua 6 facets vượt giới hạn 24KB. Diamond cho phép nâng cấp từng facet riêng, shared storage giảm gas, function routing qua selector → facet.

## Contract Addresses (Unichain Sepolia Testnet)

| Contract | Address |
|---|---|
| Diamond | 0xF38a265E6e4F57D000a1CC08004da5B4A380B08A |
| Hook | 0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0 |
| Exchange | 0xa202abCb2A358c0862B2dA76b553398339F2C638 |
| Router | 0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7 |
| Oracle | 0x699A8C74663b1C852E195b2ffa00D5965E992Cf3 |
| USDC | 0x12fd156C8b5F2901BA2781d97db84AaC56b2b911 |
| Pool Manager | 0x00B036B58a818B1BC34d502D3fE730Db729e62AC |

Chain ID: 1301 (Unichain Sepolia)
