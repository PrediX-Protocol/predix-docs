# 35. Trading Integration

## Approve USDC
Trước khi trade, approve USDC cho contract tương ứng:
Router: cho market orders (buyYes/sellYes/buyNo/sellNo)
Exchange: cho limit orders (placeOrder)
Diamond: cho split/merge
await usdc.approve(ROUTER_ADDRESS, ethers.MaxUint256);

## Market Order Flow (Buy YES)
// 1. Get quote
const expectedOut = await router.quoteBuyYes(marketId, usdcAmount);

// 2. Set slippage (2%)
const minOut = expectedOut * 98n / 100n;

// 3. Execute
const tx = await router.buyYes(marketId, usdcAmount, minOut, wallet.address, deadline);

## Limit Order Flow
// Approve USDC to Exchange
await usdc.approve(EXCHANGE_ADDRESS, ethers.MaxUint256);

// Place limit order: Buy YES at $0.55
const side = 0; // BUY_YES
const price = ethers.parseUnits("0.55", 6); // 550000
const amount = ethers.parseUnits("100", 6); // 100 tokens

const tx = await exchange.placeOrder(marketId, side, price, amount);
const receipt = await tx.wait();

## Cancel Order
await exchange.cancelOrder(orderId);

## Get Quotes Before Trading
const yesCost = await router.quoteBuyYes(marketId, ethers.parseUnits("100", 6));
const noCost = await router.quoteBuyNo(marketId, ethers.parseUnits("100", 6));
console.log("100 USDC buys:", ethers.formatUnits(yesCost, 6), "YES");
console.log("100 USDC buys:", ethers.formatUnits(noCost, 6), "NO");
