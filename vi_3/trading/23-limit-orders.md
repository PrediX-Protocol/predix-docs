# 23. Limit Orders

## Đặt Lệnh
exchange.placeOrder(marketId, side, price, amount) → (orderId, filledAmount)
Side enum: BUY_YES=0, SELL_YES=1, BUY_NO=2, SELL_NO=3
Price: $0,01–$0,99 theo bước $0,01 (10000–990000 trong 6 decimals)
Min amount: 1 token ($1,00)
Max orders: 50 per user per market

## Huỷ Lệnh
exchange.cancelOrder(orderId)
Chủ lệnh hoặc bất kỳ ai nếu market đã expired.

## View Functions
getBestPrices(marketId) → (bestBidYes, bestAskYes, bestBidNo, bestAskNo)
getOrderBook(marketId, depth) → (yesBids, yesAsks, noBids, noAsks)
getDepthAtPrice(marketId, side, price) → amount
