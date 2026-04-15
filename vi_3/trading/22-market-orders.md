# 22. Market Orders

## Buy YES
router.buyYes(marketId, usdcIn, minYesOut, recipient, deadline) → yesOut

## Sell YES
router.sellYes(marketId, yesIn, minUsdcOut, recipient, deadline) → usdcOut

## Buy NO
router.buyNo(marketId, usdcIn, mintAmount, minNoOut, recipient, deadline) → noOut
Cơ chế: Split USDC → YES+NO, bán YES trên AMM, giữ NO.

## Sell NO
router.sellNo(marketId, noIn, minUsdcOut, recipient, deadline) → usdcOut
Cơ chế: Mua YES trên AMM, merge YES+NO → USDC.

## Slippage Protection
Tham số minOut đảm bảo số token tối thiểu nhận được. Nếu không đạt → revert.

## Deadline
Thường đặt 5 phút. Nếu giao dịch chưa confirmed → revert.

## Quote Functions
quoteBuyYes(marketId, usdcIn) → expectedYesOut
quoteSellYes(marketId, yesIn) → expectedUsdcOut
quoteBuyNo(marketId, usdcIn) → expectedNoOut
quoteSellNo(marketId, noIn) → expectedUsdcOut
