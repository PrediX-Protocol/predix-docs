# 4. Tổng Quan Sản Phẩm

### 4.1 Kiến Trúc Protocol

PrediX là prediction market hoàn toàn on-chain trên Unichain (L2 OP Stack). Non-custodial — người dùng luôn giữ key riêng. Năm module smart contract cốt lõi:

* **Diamond Proxy (EIP-2535):** 6 facets — Cut, Loupe, AccessControl, Ownable, Pausable, Market. Nâng cấp module không cần redeploy
* **Exchange (CLOB):** Sổ lệnh on-chain 99-tick bitmap, limit order $0,01–$0,99 theo bước $0,01
* **Router (Smart Router):** Tổng hợp AMM + CLOB, route tối ưu, flash accounting đa bước nguyên tử
* **Hook (Uniswap v4):** Phí dynamic time-decay, chống sandwich, buyNo/sellNo nguyên tử
* **Oracle (Đa Oracle):** ManualOracleAdapter, ChainlinkAdapter, UMACrossChainAdapter

### 4.2 Tại Sao Chọn Unichain

Đã đánh giá Polygon PoS, Arbitrum, Base, Solana, Unichain. Chọn Unichain vì:

* 200ms Flashblocks (nhanh gấp 10x Polygon 2s)
* Uniswap v4 native (không cần tự deploy)
* Revert protection (giao dịch thất bại = $0 gas)
* TEE ordering giảm MEV
* USDC native qua Circle CCTP V2

### 4.3 ERC-20 Outcome Tokens

Quyết định kiến trúc quan trọng nhất. Polymarket dùng ERC-1155 (Gnosis CTF) → không LP được trên DEX, không làm collateral, hiển thị hex hash trong ví. ERC-20 của PrediX:

* LP YES/USDC trên v4 kiếm swap fees trong khi giữ vị thế prediction
* Collateral trên lending protocols (Phase 3)
* Vault strategies và sản phẩm cấu trúc
* 6 decimal khớp USDC. Bất biến: YES supply = NO supply = totalCollateral

### 4.4 Diamond Pattern (EIP-2535)

48 source files qua 6 facets không thể nhét vào 1 contract 24KB. Nâng cấp từng facet riêng, shared storage giảm gas.

### 4.5 Protocol-Managed Liquidity

createMarket() tự động seed $5–30K cho mỗi market. Chainlink Automation rebalance. Xoay vòng vốn trả lại \~85–95% vốn seed khi resolve.

### 4.6 Cross-Chain (CCTP V2 + Hyperlane)

USDC deposit/withdraw qua Circle CCTP V2 (native mint/burn, <30 giây). Oracle cross-chain qua Hyperlane. Deposit + trade trong 1 flow UX.

### 4.7 Luồng Người Dùng (Step-by-Step)

* Kết nối ví → MetaMask/WalletConnect trên Unichain
* Nạp USDC → Trực tiếp hoặc qua CCTP V2 từ chain khác
* Chọn market → Duyệt theo category (Sports, Crypto, Politics…)
* Mua YES hoặc NO → Router tự động route qua CLOB/AMM tối ưu
* Đợi kết quả → Oracle resolve market sau khi hết hạn
* Redeem → Token thắng đổi 1:1 USDC, token thua = $0

### 4.8 Địa Chỉ Contract (Unichain Sepolia Testnet)

<table><thead><tr><th width="203">Contract</th><th>Địa chỉ</th></tr></thead><tbody><tr><td>Diamond</td><td>0xF38a265E6e4F57D000a1CC08004da5B4A380B08A</td></tr><tr><td>Hook</td><td>0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0</td></tr><tr><td>Exchange</td><td>0xa202abCb2A358c0862B2dA76b553398339F2C638</td></tr><tr><td>Router</td><td>0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7</td></tr><tr><td>Oracle</td><td>0x699A8C74663b1C852E195b2ffa00D5965E992Cf3</td></tr><tr><td>USDC</td><td>0x12fd156C8b5F2901BA2781d97db84AaC56b2b911</td></tr><tr><td>Pool Manager</td><td>0x00B036B58a818B1BC34d502D3fE730Db729e62AC</td></tr></tbody></table>

Chain ID: 1301 (Unichain Sepolia)
