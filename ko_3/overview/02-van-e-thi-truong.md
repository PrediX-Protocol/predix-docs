# 2. 시장의 문제

Prediction market은 서로 연결된 구조적 문제들을 안고 있습니다:

<figure><img src="../.gitbook/assets/prediction-market-problems.png" alt=""><figcaption></figcaption></figure>

### 2.1 유동성 Cold-Start

유동성이 없으면 트레이더가 없고, 트레이더가 없으면 유동성이 없습니다. 기존 플랫폼은 대규모 VC나 중앙화된 market maker에 의존해 초기 depth를 만들어 왔습니다.

### 2.2 자본 효율 저하

AMM 기반 prediction market (Augur, Omen)은 LP가 $0–$1 전체 범위를 커버해야 하므로, 자본의 60–70%가 실제로 사용되지 않는 구간에 묶입니다. Concentrated liquidity를 prediction market에 적용한 사례는 없었습니다.

### 2.3 중앙화된 Oracle Resolution

대부분의 플랫폼은 단일 오라클을 사용하거나 admin이 수동으로 결과를 확정합니다. 마켓 유형별로 적합한 오라클을 꽂아 쓸 수 있는 pluggable 시스템이 필요합니다.

### 2.4 사용자는 아무것도 소유하지 못함

트레이더가 모든 가치(볼륨, 수수료, 데이터)를 만들지만, 수익 분배·거버넌스·경제적 이익은 받지 못합니다. 프로토콜이 경제 가치를 100% 독점합니다.

### 2.5 중앙화 리스크

Polymarket은 중앙화된 운영자에 의존합니다. CFTC가 이미 개입한 바 있습니다. 33개국 이상(폴란드, 싱가포르, 벨기에 등)에서 금지. Kalshi는 네바다·애리조나에서 법적 분쟁에 직면해 있습니다.

### 2.6 APAC의 공백

VN, KR, JP 등 세계에서 가장 강한 게이밍·트레이딩 문화를 가진 지역에 3,800만+ 크립토 사용자가 있지만, 현지 prediction market이 없습니다.

### 2.7 단일 소스 유동성

Polymarket은 CLOB만, Augur/Gnosis는 AMM만. 양쪽을 스마트 라우팅으로 결합한 플랫폼은 아직 없습니다.
