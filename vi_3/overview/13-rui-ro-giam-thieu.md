# 13. Rủi Ro & Giảm Thiểu

<table><thead><tr><th width="224">Rủi ro</th><th width="129">Mức độ</th><th>Giảm thiểu</th></tr></thead><tbody><tr><td>Cold-Start thanh khoản</td><td>CAO</td><td>Protocol LP $5–30K/market, CoinCraze 100K+, Chainlink rebalance</td></tr><tr><td>Lỗ hổng smart contract</td><td>CAO</td><td>264 tests, 3 audit AI, Slither 0 lỗi, Fuzz 14×10K, 31 E2E on-chain</td></tr><tr><td>Pháp lý</td><td>CAO</td><td>Non-custodial, geo-block US/SG, BVI foundation</td></tr><tr><td>Thao túng oracle</td><td>TB</td><td>Đa oracle, commit-reveal, slash, escalation multi-sig</td></tr><tr><td>Giá token giảm</td><td>TB</td><td>50% buyback-burn, real yield USDC, TGE sau PMF</td></tr><tr><td>Cạnh tranh Polymarket</td><td>TB</td><td>Focus APAC, ERC-20 vs ERC-1155, gauge voting, NFT gamification</td></tr><tr><td>Volume thấp</td><td>TB</td><td>FIFA WC catalyst, văn hoá APAC, hoà vốn $4,5–7M khả thi</td></tr><tr><td>Thực thi team</td><td>THẤP</td><td>Contracts deployed, 264 tests, SP Labs đồng tài trợ 40%</td></tr></tbody></table>

## Kiến Trúc An Ninh

* **Safety Caps:** TVL, per-trade, per-market (configurable)
* **Pause Module:** Tạm dừng từng facet hoặc toàn bộ
* **Emergency Resolve:** 7 ngày delay sau endTime
* **Refund Mode:** Hoàn USDC tỷ lệ
* **Anti-Sandwich:** 200ms Flashblocks + TEE ordering
* **Reentrancy Guards:** 3 loại bảo vệ
