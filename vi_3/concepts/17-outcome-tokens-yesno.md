# 17. Outcome Tokens (YES/NO)

## Đặc Điểm Kỹ Thuật

- **Chuẩn:** ERC-20 + ERC-2612 Permit (gasless approvals)
- **Decimals:** 6 (khớp USDC)
- **Mint/Burn:** Chỉ Diamond contract có quyền (onlyFactory)
- **Bất biến cung:** YES supply = NO supply = totalCollateral

## Tại Sao ERC-20?

Polymarket dùng ERC-1155 (Gnosis CTF) → không LP, không collateral, hiển thị hex trong ví. ERC-20 PrediX tích hợp native toàn bộ DeFi: LP trên DEX, collateral trên lending, vault strategies.

## Vòng Đời Token

- **Mint:** Split $1 USDC → 1 YES + 1 NO
- **Trade:** Mua/bán trên CLOB hoặc AMM
- **LP:** Cung cấp thanh khoản YES/USDC kiếm fees
- **Redeem:** Sau resolve, token thắng đổi 1:1 USDC
