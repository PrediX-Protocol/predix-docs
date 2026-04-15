# 44. FAQ

PrediX là gì? PrediX Protocol -“Where narratives become markets” — là prediction market phi tập trung trên Uniswap v4 (Unichain L2). Protocol biến mọi narrative (thể thao, chính trị, crypto, văn hoá) thành thị trường giao dịch on-chain, kết hợp AMM + CLOB, ERC-20 outcome tokens, Diamond proxy.
Làm sao bắt đầu trade? Kết nối ví → nạp USDC (hoặc CCTP V2 từ chain khác) → chọn market → mua YES hoặc NO → đợi kết quả → redeem.

- Khi market resolve thì sao? Token thắng đổi 1:1 USDC. Token thua = $0. Gọi redeemMarketTokens() để nhận USDC.
- Có thể mất hơn số đầu tư không? Không. Tối đa mất 100% vị thế (token thua = $0). Không có đòn bẩy hay liquidation.

Giá được xác định như thế nào? Qua supply/demand trên CLOB + AMM. Giá YES phản ánh xác suất thị trường tin sự kiện xảy ra.

- Split/Merge là gì? Split: $1 USDC → 1 YES + 1 NO. Merge: 1 YES + 1 NO → $1 USDC. Neo giá YES + NO ≈ $1.
- Phí bao nhiêu? AMM: 0,5–5% (dynamic theo thời gian). CLOB: 0–1% taker. Không maker fee.
- PrediX đã audit chưa? Có. 264 tests, 3 AI audits kiểm chéo, Slither 0 lỗi, fuzz testing 14×10K runs, 31 E2E on-chain.
- PrediX ở chain nào? Unichain Sepolia Testnet (Chain ID: 1301). Mainnet launch Q2/2026.
- Lấy USDC test ở đâu? Unichain Sepolia faucet hoặc contact team qua Discord.
