# 30. Router (Smart Router)

## Trading Functions

```
buyYes(marketId, usdcIn, minYesOut, recipient, deadline) → yesOut
sellYes(marketId, yesIn, minUsdcOut, recipient, deadline) → usdcOut
buyNo(marketId, usdcIn, mintAmount, minNoOut, recipient, deadline) → noOut
sellNo(marketId, noIn, minUsdcOut, recipient, deadline) → usdcOut
```

## Quote Functions

```solidity
quoteBuyYes(marketId, usdcIn) → uint256
quoteSellYes(marketId, yesIn) → uint256
quoteBuyNo(marketId, usdcIn) → uint256
quoteSellNo(marketId, noIn) → uint256
```

## Flash Accounting

Router sử dụng Uniswap v4 unlock callback cho thao tác đa bước nguyên tử. buyNo thực hiện: split → swap → transfer trong 1 transaction.

## Virtual NO Pricing

Router xử lý mua/bán NO qua split+swap+merge tự động. User chỉ cần gọi buyNo/sellNo, phức tạp nội bộ được abstract.
