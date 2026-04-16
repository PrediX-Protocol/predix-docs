# References & Benchmarks

PrediX không phát minh lại bánh xe — protocol kết hợp các cơ chế đã proven từ những project thành công nhất trong DeFi, áp dụng cho vertical prediction market. Dưới đây là bảng tham chiếu đầy đủ:

### 1 Token Mechanics References

| Cơ chế PrediX                  | Protocol tham chiếu                          | Cách áp dụng                                       | Kết quả ở protocol gốc                  |
| ------------------------------ | -------------------------------------------- | -------------------------------------------------- | --------------------------------------- |
| Fee Buyback-Burn (30% revenue) | Hyperliquid (97% fee → buyback)              | Thu hẹp tỷ lệ, cân bằng với staker yield           | $49B FDV, $716M burn 2025               |
| veToken Staking + Gauge Voting | Curve Finance (veCRV)                        | Áp dụng gauge voting cho prediction market lần đầu | $4B TVL locked, “Curve Wars” ecosystem  |
| LP Yield Boost (1,5x–2,5x)     | Curve/Balancer (veBAL boost)                 | Khuyến khích khoá dài hạn + cung cấp thanh khoản   | Balancer: 80%+ BAL bị khoá              |
| Real Yield Staking (50% fee)   | dYdX V4 (100% fee → staker), GMX (30% → GLP) | Mô hình 50/30/20 cân bằng                          | dYdX: $1B+ volume/ngày. GMX: $150M TVL  |
| Oracle Dispute Bond            | UMA (Optimistic Oracle + DVM)                | Commit-reveal thay vì optimistic window            | UMA: 99,9% dispute resolution chính xác |
| Market Creation Bond           | Azuro (pool creation), Hyperliquid HIP-3     | Kết hợp bond + revenue share cho creator           | Azuro: 100+ data providers              |
| Collateral Yield               | GMX (GLP yield sharing)                      | vePREDIX holders chia yield từ idle USDC           | GMX: 70% ETH fees → staker              |
| Fee Tiers by Staking           | Hyperliquid, BNB (Binance)                   | Kết hợp staking + volume threshold                 | Binance: BNB discount là driver chính   |

### 2 NFT & Gamification References

| Cơ chế PrediX                     | Protocol tham chiếu                      | Cách áp dụng                                           | Kết quả ở gốc                    |
| --------------------------------- | ---------------------------------------- | ------------------------------------------------------ | -------------------------------- |
| Trader Passport (Soulbound)       | Galxe Passport, Lens Protocol            | Identity layer chuyên biệt cho PM traders              | Galxe: 15M+ passports            |
| Achievement Badges → Fee discount | Blur Season badges, Rabbithole           | Badge gắn trực tiếp utility (phí) thay vì cosmetic     | Blur: 80% NFT market share       |
| Season Competition                | Fortnite Battle Pass, Hyperliquid Points | Season pass model cho prediction market                | Fortnite: $5,8B từ battle pass   |
| Referral NFT → Fee sharing        | Blur referral, Hyperliquid referral      | NFT tiến hoá + perpetual fee share                     | Hyperliquid: 5% referral program |
| LP Position NFT                   | Uniswap V3 LP NFTs                       | Tokenize LP prediction market, enable secondary market | Uniswap V3: $5B+ TVL qua LP NFTs |
| Market Founder NFT                | Mirror Writing NFTs, Azuro pool creator  | Dynamic art + perpetual revenue share cho creator      | Mirror: creator economy on-chain |

### 3 Architecture References

| Thành phần PrediX        | Protocol tham chiếu                     | Tại sao chọn                                   |
| ------------------------ | --------------------------------------- | ---------------------------------------------- |
| Hybrid AMM+CLOB          | Hyperliquid (CLOB), Uniswap (AMM)       | Kết hợp ưu điểm cả hai - chưa PM nào làm được  |
| ERC-20 Outcome Tokens    | Azuro (ERC-20) vs Polymarket (ERC-1155) | DeFi composable - LP, lending, vault           |
| Diamond Proxy (EIP-2535) | Aavegotchi, Louper                      | Nâng cấp module, vượt giới hạn 24KB            |
| Uniswap v4 Hooks         | Uniswap v4 ecosystem                    | Dynamic fees, anti-sandwich, pool registration |
| CCTP V2 Cross-chain      | Circle CCTP                             | Native USDC mint/burn, <30 giây                |
| Protocol-Managed LP      | Olympus DAO (protocol-owned liquidity)  | Auto-seed $5–30K/market, giải quyết cold-start |

### 4 Fundraising Comparables

<table><thead><tr><th width="136">Protocol</th><th width="123">Raise</th><th width="118">FDV khi raise</th><th width="153">Hiện tại</th><th>Bài học cho PrediX</th></tr></thead><tbody><tr><td>Polymarket</td><td>$4M seed (2020)</td><td>$25M</td><td>~$20B valuation</td><td>Volume = moat. Nhưng: banned 33+ countries</td></tr><tr><td>Kalshi</td><td>Tier-1 VC</td><td>~$100M</td><td>$22B, $1,5B rev/năm</td><td>CFTC license = monopoly. Nhưng: $500K+ ops/tháng</td></tr><tr><td>Opinion (OPN)</td><td>$25M total</td><td>~$50M seed</td><td>TGE $450M FDV</td><td>Binance Labs = credibility. Nhưng: dump 17,8% D1</td></tr><tr><td>PredictEX</td><td>$10M</td><td>~$30–50M</td><td>Private beta</td><td>Exchange VCs = distribution. 1 exchange VC = $5M marketing</td></tr><tr><td>Azuro</td><td>$11M Series A</td><td>~$30M</td><td>Multi-chain</td><td>Infrastructure play. Nhưng: token utility yếu</td></tr><tr><td>Novig</td><td>$18M Series A</td><td>~$80M+</td><td>Sports P2P PM</td><td>Validates sports PM vertical. US-focused</td></tr><tr><td>PrediX</td><td>$1,5–2M</td><td>$15–20M</td><td>Contracts xong</td><td>FDV thấp nhất + break-even thấp nhất trong category</td></tr></tbody></table>
