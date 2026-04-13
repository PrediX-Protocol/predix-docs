---
description: 몇 분 안에 PrediX에서 거래 또는 개발 시작하기
---

# 빠른 시작

## 트레이더용

### 1단계: 지갑 연결

Unichain Sepolia를 지갑에 추가:

| 파라미터 | 값 |
| --------- | ----- |
| Network Name | Unichain Sepolia |
| RPC URL | `https://sepolia.unichain.org` |
| Chain ID | `1301` |
| Currency Symbol | ETH |
| Block Explorer | `https://sepolia.uniscan.xyz` |

### 2단계: 테스트 USDC 받기

PrediX faucet에서 테스트넷 USDC를 받거나 테스트넷에서 직접 민트하세요.

### 3단계: USDC 승인

Router 컨트랙트가 USDC를 사용할 수 있도록 승인:

```
USDC Address: 0x12fd156C8b5F2901BA2781d97db84AaC56b2b911
Router Address: 0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7
```

### 4단계: 거래

- **YES 매수**: `router.buyYes(marketId, usdcAmount, minYesOut, yourAddress, deadline)` 호출
- **NO 매수**: `router.buyNo(marketId, usdcAmount, mintAmount, minNoOut, yourAddress, deadline)` 호출

### 5단계: 해결 후 수령

마켓이 해결되면, `diamond.redeemMarketTokens(marketId)`를 호출하여 수익을 수령하세요.

---

## 개발자용

### 컨트랙트 주소 (Unichain Sepolia)

| 컨트랙트 | 주소 |
| -------- | ------- |
| Diamond | `0xF38a265E6e4F57D000a1CC08004da5B4A380B08A` |
| Hook | `0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0` |
| Exchange | `0xa202abCb2A358c0862B2dA76b553398339F2C638` |
| Router | `0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7` |
| Oracle | `0x699A8C74663b1C852E195b2ffa00D5965E992Cf3` |
| USDC | `0x12fd156C8b5F2901BA2781d97db84AaC56b2b911` |
| Pool Manager | `0x00B036B58a818B1BC34d502D3fE730Db729e62AC` |

### 빠른 코드 예시

```typescript
import { ethers } from "ethers";

const provider = new ethers.JsonRpcProvider("https://sepolia.unichain.org");
const signer = new ethers.Wallet(PRIVATE_KEY, provider);

const diamond = new ethers.Contract(
  "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A",
  DIAMOND_ABI,
  signer
);

// 마켓 읽기
const market = await diamond.getMarket(marketId);
console.log("Question:", market.question);
console.log("YES token:", market.yesToken);
console.log("Total collateral:", ethers.formatUnits(market.totalCollateral, 6));
```

---

**다음**: [개발자 빠른 시작](../developers/quickstart.md) · [컨트랙트 주소](../developers/addresses.md) · [거래 통합](../developers/trading-integration.md)
