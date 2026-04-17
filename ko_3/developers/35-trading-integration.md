# 35. Trading Integration

## Approve USDC

거래 전, 해당 컨트랙트에 USDC를 approve:

- **Router:** market order용 (buyYes/sellYes/buyNo/sellNo)
- **Exchange:** limit order용 (placeOrder)
- **Diamond:** split/merge용

```typescript
await usdc.approve(ROUTER_ADDRESS, ethers.MaxUint256);
```

## Market Order Flow (Buy YES)

```typescript
// 1. Quote 받기
const expectedOut = await router.quoteBuyYes(marketId, usdcAmount);

// 2. 슬리피지 설정 (2%)
const minOut = expectedOut * 98n / 100n;

// 3. 실행
const tx = await router.buyYes(marketId, usdcAmount, minOut, wallet.address, deadline);
```

## Limit Order Flow

```typescript
// Exchange에 USDC approve
await usdc.approve(EXCHANGE_ADDRESS, ethers.MaxUint256);

// Limit order 등록: $0.55에 YES 매수
const side = 0; // BUY_YES
const price = ethers.parseUnits("0.55", 6); // 550000
const amount = ethers.parseUnits("100", 6); // 100 tokens

const tx = await exchange.placeOrder(marketId, side, price, amount);
const receipt = await tx.wait();
```

## Cancel Order

```typescript
await exchange.cancelOrder(orderId);
```

## Get Quotes Before Trading

```typescript
const yesCost = await router.quoteBuyYes(marketId, ethers.parseUnits("100", 6));
const noCost = await router.quoteBuyNo(marketId, ethers.parseUnits("100", 6));
console.log("100 USDC buys:", ethers.formatUnits(yesCost, 6), "YES");
console.log("100 USDC buys:", ethers.formatUnits(noCost, 6), "NO");
```
