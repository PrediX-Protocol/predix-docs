# 16. Prediction Markets

### Prediction Market이란?

Prediction market은 참가자가 미래 이벤트의 결과를 기반으로 포지션을 매매하는 시장입니다. 각 포지션의 가격은 시장이 해당 이벤트가 발생할 것으로 믿는 집단적 확률을 반영합니다.

### 가격은 확률을 반영

YES 토큰이 $0.70에 거래된다면, 시장은 이벤트 발생 확률을 70%로 암묵 표시합니다. 이벤트가 발생하면 YES 토큰 하나당 $1.00. 아니면 YES = $0, NO = $1.00.

### 불변식 YES + NO ≈ $1

$1 USDC를 언제든 1 YES + 1 NO로 Split할 수 있기 때문에, YES + NO 합계는 항상 arbitrage로 인해 $1 근처에 고정됩니다. YES = $0.60, NO = $0.50 (합계 $1.10)이면 → arbitrager가 둘 다 팔아 $0.10 수익, 가격을 $1로 복귀시킵니다.

## 역사

* **Augur (2018):** 최초의 탈중앙화 PM, 사용자에게 너무 복잡
* **Polymarket (2020):** UX 단순화, 2024년 미국 대선으로 폭발적 성장
* **Kalshi (2021):** CFTC 등록, 미국 기관 중심
* **PrediX (2026):** 하이브리드 AMM+CLOB, ERC-20 composable, APAC-native
