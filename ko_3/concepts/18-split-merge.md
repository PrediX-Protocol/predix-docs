# 18. Split & Merge

### Split: $1 USDC → 1 YES + 1 NO

Diamond 컨트랙트로 USDC를 예치합니다. 1 YES + 1 NO를 받습니다. USDC는 담보로 보관. YES = NO = totalCollateral 공급량이 해당만큼 증가.

### Merge: 1 YES + 1 NO → $1 USDC

1 YES + 1 NO를 Diamond에 반환. $1 USDC를 받음. 토큰은 burn. 공급량 감소.

### 왜 중요한가?

* **가격 고정:** YES + NO ≈ $1, arbitrage로 유지
* **유동성 생성:** LP가 YES 토큰을 필요로 함 → 먼저 Split
* **전략:** Split + NO 매도로 YES 매수 (또는 반대)

### Group Split/Merge (Categories)

* **GroupSplit:** USDC 예치, 카테고리 내 모든 outcome의 토큰 수령
* **GroupMerge:** 전체 토큰 세트 반환, USDC 수령
