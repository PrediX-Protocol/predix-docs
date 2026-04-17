# 해결책

PrediX는 각 문제를 구체적인 메커니즘 설계로 해결합니다:

### 1 하이브리드 트레이딩 엔진 (AMM + CLOB)

Smart Router가 Uniswap v4 AMM과 온체인 CLOB의 유동성을 통합합니다. AMM은 즉시 가격과 수동적 depth를 제공합니다. CLOB은 정확한 limit order를 가능하게 합니다. AMM이 첫날부터 기본 유동성을 보장하고, CLOB이 전문 market maker를 끌어들입니다.

### 2 탈중앙화 Oracle Resolution

PRX 홀더가 토큰을 스테이킹해 commit-reveal 투표로 분쟁 결과를 결정합니다. 올바른 투표는 dispute bond로 보상받고, 틀린 투표는 슬래시됩니다. 가격 기반 시장은 Chainlink 피드로 자동 resolve됩니다.

### 3 Real Yield — 수익 공유

거래 수수료의 50%를 PRX 스테이커에게 USDC로 분배(인플레이션 토큰 아님). 추가로 30%는 PRX 자동 buyback-and-burn에 사용됩니다.

### 4 Market을 위한 Gauge Voting

PRX를 락업 → vePRX (1–12개월). vePRX 홀더는 매주 어떤 마켓이 LP 인센티브를 받을지 투표합니다. 표를 많이 받는 마켓 → 더 깊은 유동성 → 더 높은 볼륨 → 더 많은 수수료. prediction market 최초의 Curve Wars 메커니즘입니다.

### 5. APAC-Native 배포

CoinCraze(VN, KR, JP 3만+ 멤버)와 MegaPro가 기존 배포 채널을 제공합니다. NFT 게임화가 APAC 게임 문화에 부합합니다. 첫날부터 베트남어·한국어·일본어 현지화.

### 요약

PrediX는 5개 주요 해결책 레이어를 결합합니다: 하이브리드 유동성, 탈중앙화 오라클, real yield, gauge voting, APAC-native 배포. 각 레이어는 prediction market의 핵심 병목을 해결합니다. 함께 작동할 때, 더 깊은 유동성, 더 나은 UX, 더 지속 가능한 경제적 인센티브를 만듭니다.
