# 15. References & Benchmarks

PrediX không phát minh lại bánh xe — protocol kết hợp các cơ chế đã proven từ những project thành công nhất trong DeFi, áp dụng cho vertical prediction market. Dưới đây là bảng tham chiếu đầy đủ:

## 15.1 Token Mechanics References

| Cơ chế PrediX | Protocol tham chiếu | Cách áp dụng | Kết quả ở protocol gốc |
|---|---|---|---|
| Fee Buyback-Burn (30% revenue) | Hyperliquid (97% fee → buyback) | Thu hẹp tỷ lệ, cân bằng với staker yield | $49B FDV, $716M burn 2025 |
| veToken Staking + Gauge Voting | Curve Finance (veCRV) | Áp dụng gauge voting cho prediction market lần đầu | $4B TVL locked, “Curve Wars” ecosystem |
| LP Yield Boost (1,5x–2,5x) | Curve/Balancer (veBAL boost) | Khuyến khích khoá dài hạn + cung cấp thanh khoản | Balancer: 80%+ BAL bị khoá |
| Real Yield Staking (50% fee) | dYdX V4 (100% fee → staker), GMX (30% → GLP) | Mô hình 50/30/20 cân bằng | dYdX: $1B+ volume/ngày. GMX: $150M TVL |
| Oracle Dispute Bond | UMA (Optimistic Oracle + DVM) | Commit-reveal thay vì optimistic window | UMA: 99,9% dispute resolution chính xác |
| Market Creation Bond | Azuro (pool creation), Hyperliquid HIP-3 | Kết hợp bond + revenue share cho creator | Azuro: 100+ data providers |
| Collateral Yield | GMX (GLP yield sharing) | vePREDIX holders chia yield từ idle USDC | GMX: 70% ETH fees → staker |
| Fee Tiers by Staking | Hyperliquid, BNB (Binance) | Kết hợp staking + volume threshold | Binance: BNB discount là driver chính |

## 15.2 NFT & Gamification References

| Cơ chế PrediX | Protocol tham chiếu | Cách áp dụng | Kết quả ở gốc |
|---|---|---|---|
| Trader Passport (Soulbound) | Galxe Passport, Lens Protocol | Identity layer chuyên biệt cho PM traders | Galxe: 15M+ passports |
| Achievement Badges → Fee discount | Blur Season badges, Rabbithole | Badge gắn trực tiếp utility (phí) thay vì cosmetic | Blur: 80% NFT market share |
| Season Competition | Fortnite Battle Pass, Hyperliquid Points | Season pass model cho prediction market | Fortnite: $5,8B từ battle pass |
| Referral NFT → Fee sharing | Blur referral, Hyperliquid referral | NFT tiến hoá + perpetual fee share | Hyperliquid: 5% referral program |
| LP Position NFT | Uniswap V3 LP NFTs | Tokenize LP prediction market, enable secondary market | Uniswap V3: $5B+ TVL qua LP NFTs |
| Market Founder NFT | Mirror Writing NFTs, Azuro pool creator | Dynamic art + perpetual revenue share cho creator | Mirror: creator economy on-chain |

## 15.3 Architecture References

| Thành phần PrediX | Protocol tham chiếu | Tại sao chọn |
|---|---|---|
| Hybrid AMM+CLOB | Hyperliquid (CLOB), Uniswap (AMM) | Kết hợp ưu điểm cả hai  - chưa PM nào làm được |
| ERC-20 Outcome Tokens | Azuro (ERC-20) vs Polymarket (ERC-1155) | DeFi composable  - LP, lending, vault |
| Diamond Proxy (EIP-2535) | Aavegotchi, Louper | Nâng cấp module, vượt giới hạn 24KB |
| Uniswap v4 Hooks | Uniswap v4 ecosystem | Dynamic fees, anti-sandwich, pool registration |
| CCTP V2 Cross-chain | Circle CCTP | Native USDC mint/burn, <30 giây |
| Protocol-Managed LP | Olympus DAO (protocol-owned liquidity) | Auto-seed $5–30K/market, giải quyết cold-start |

## 15.4 Fundraising Comparables

| Protocol | Raise | FDV khi raise | Hiện tại | Bài học cho PrediX |
|---|---|---|---|---|
| Polymarket | $4M seed (2020) | $25M | ~$20B valuation | Volume = moat. Nhưng: banned 33+ countries |
| Kalshi | Tier-1 VC | ~$100M | $22B, $1,5B rev/năm | CFTC license = monopoly. Nhưng: $500K+ ops/tháng |
| Opinion (OPN) | $25M total | ~$50M seed | TGE $450M FDV | Binance Labs = credibility. Nhưng: dump 17,8% D1 |
| PredictEX | $10M | ~$30–50M | Private beta | Exchange VCs = distribution. 1 exchange VC = $5M marketing |
| Azuro | $11M Series A | ~$30M | Multi-chain | Infrastructure play. Nhưng: token utility yếu |
| Novig | $18M Series A | ~$80M+ | Sports P2P PM | Validates sports PM vertical. US-focused |
| PrediX | $1,5–2M | $15–20M | Contracts xong | FDV thấp nhất + break-even thấp nhất trong category |
