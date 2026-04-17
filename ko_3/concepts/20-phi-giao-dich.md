# 20. 거래 수수료

## AMM Dynamic Fees

Market이 만료에 가까워질수록 LP 보호를 위해 수수료가 증가:

<table><thead><tr><th>남은 시간</th><th width="249">수수료</th><th>이유</th></tr></thead><tbody><tr><td>> 7일</td><td>0.5%</td><td>초기 거래 활성화</td></tr><tr><td>3–7일</td><td>1.0%</td><td>정보 가치 상승</td></tr><tr><td>1–3일</td><td>2.0%</td><td>Toxic flow 증가</td></tr><tr><td>&#x3C; 24시간</td><td>5.0%</td><td>Adverse selection 최고조</td></tr></tbody></table>

### CLOB Fees

0%로 시작 (market maker 유치). M+7부터 taker 1%로 상승.

대칭 공식: fee = feeRate × min(price, 1−price) × amount.

### MINT/MERGE Surplus

CLOB이 MINT(BUY\_YES + BUY\_NO, 합 > $1)로 매칭하면, 초과분은 프로토콜 수익. 예: $0.72 + $0.30 = $1.02, 초과 $0.02.
