# 26. Virtual NO Pricing

## Vấn Đề
NO token không có pool AMM riêng. Chỉ YES/USDC pool tồn tại.

## Cách Giải Quyết
Mua NO: Split USDC → YES+NO, bán YES trên AMM, giữ NO
Bán NO: Mua YES trên AMM, merge YES+NO → USDC
Toàn bộ nguyên tử qua flash accounting Uniswap v4. Từ góc user: trả USDC, nhận NO (hoặc ngược lại).

## Quan Hệ Giá
NO price ≈ 1 − YES price. Duy trì bởi arbitrage: nếu lệch → arbitrageur split/merge để kiếm lợi nhuận, kéo giá về.

# PART IV: SMART CONTRACTS
