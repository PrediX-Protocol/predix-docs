# 작동 원리

### 1 거래 메커니즘

사용자는 USDC로 outcome token을 거래합니다. 각 마켓은 결과를 나타내는 토큰을 가집니다 — 단순한 YES/NO 마켓 또는 선택지별 토큰을 가진 multi-outcome(Categories) 마켓. 오직 YES만 직접 AMM 풀(YES/USDC)을 가집니다. NO 토큰은 YES 가격을 기반으로 가격이 매겨집니다:

* **NO 매수:** USDC Split → YES+NO, AMM에서 YES 판매, NO 보유
* **NO 매도:** AMM에서 YES 매수, YES+NO merge → USDC
* Uniswap v4 flash accounting으로 원자 실행
* **가격 관계:** NO ≈ 1 − YES, arbitrage로 유지

### 2 Smart Router

Router는 CLOB을 먼저 확인 → AMM → 큰 주문은 양쪽으로 분할.

예시: YES $100 매수 → CLOB에서 $60 + AMM에서 $40. Quote 함수로 미리 확인 가능.

### 3 CLOB Matching Engine

세 가지 매칭 모드:

* **COMPLEMENTARY:** BUY\_YES ↔ SELL\_YES 직접 매칭
* **MINT:** BUY\_YES + BUY\_NO, 합이 ≥ $1.00일 때. 초과분 = 프로토콜 수익
* **MERGE:** SELL\_YES + SELL\_NO, 합이 ≤ $1.00일 때. Merge → USDC
* **가격 개선:** $0.70에 매수, $0.50에 체결 → $0.20 환불. 99-tick 비트맵, $0.01 단위. 최소 $1.00. 사용자/마켓당 최대 50개 주문.

### 4 Dynamic Fee 스케줄

<table><thead><tr><th width="174.46484375">만료까지 남은 시간</th><th width="157.30078125">AMM 수수료</th><th>이유</th></tr></thead><tbody><tr><td>> 7일</td><td>0.5%</td><td>초기 거래 활성화</td></tr><tr><td>3–7일</td><td>1.0%</td><td>정보 가치 상승</td></tr><tr><td>1–3일</td><td>2.0%</td><td>LP의 toxic flow 보상</td></tr><tr><td>&#x3C; 24시간</td><td>5.0%</td><td>만료 직전 LP 보호</td></tr></tbody></table>

CLOB: 0% 시작 → 1% taker. 공식: fee = feeRate × min(price, 1−price) × quantity.

### 5 Split & Merge

* **Split:** $1 USDC → 1 YES + 1 NO
* **Merge:** 1 YES + 1 NO → $1 USDC
* YES + NO ≈ $1 불변식을 고정, arbitrage로 가격 유지

### 6 Market 수명주기

* 생성 → 활성 → 만료 → 확정 → 환급 가능
* **일반 확정:** Oracle resolve → 누구나 resolveMarket() 호출
* **긴급 확정:** oracle 실패 시 endTime 7일 후 OPERATOR가 resolve
* **환불:** 확정 불가 시 보유량 비율대로 USDC 환급

### 7 Multi-Outcome Markets (Categories)

Categories는 관련 마켓을 하나의 multi-outcome 이벤트로 그룹화합니다(예: "2028년 미국 대통령" — 후보별 마켓). GroupSplit/GroupMerge로 일괄 처리. resolveCategory(categoryId, winningIndex)로 카테고리 전체 확정.
