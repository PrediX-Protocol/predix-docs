# 13. Rủi Ro & Giảm Thiểu

| Rủi ro | Mức độ | Giảm thiểu |
|---|---|---|
| Cold-Start thanh khoản | CAO | Protocol LP $5–30K/market, CoinCraze 100K+, Chainlink rebalance |
| Lỗ hổng smart contract | CAO | 264 tests, 3 audit AI, Slither 0 lỗi, Fuzz 14×10K, 31 E2E on-chain |
| Pháp lý | CAO | Non-custodial, geo-block US/SG, BVI foundation |
| Thao túng oracle | TB | Đa oracle, commit-reveal, slash, escalation multi-sig |
| Giá token giảm | TB | 50% buyback-burn, real yield USDC, TGE sau PMF |
| Cạnh tranh Polymarket | TB | Focus APAC, ERC-20 vs ERC-1155, gauge voting, NFT gamification |
| Volume thấp | TB | FIFA WC catalyst, văn hoá APAC, hoà vốn $4,5–7M khả thi |
| Thực thi team | THẤP | Contracts deployed, 264 tests, SP Labs đồng tài trợ 40% |

## Kiến Trúc An Ninh

- **Safety Caps:** TVL, per-trade, per-market (configurable)
- **Pause Module:** Tạm dừng từng facet hoặc toàn bộ
- **Emergency Resolve:** 7 ngày delay sau endTime
- **Refund Mode:** Hoàn USDC tỷ lệ
- **Anti-Sandwich:** 200ms Flashblocks + TEE ordering
- **Reentrancy Guards:** 3 loại bảo vệ
