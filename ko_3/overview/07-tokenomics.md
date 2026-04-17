# Tokenomics

총 공급량: 1,000,000,000 PRX | 시작 가격: $0.015 | TGE FDV: $15M

### 1 토큰 분배

<table><thead><tr><th width="248">그룹</th><th width="96">%</th><th width="111">Tokens</th><th>Vesting</th></tr></thead><tbody><tr><td>Community &#x26; Ecosystem</td><td>35%</td><td>350M</td><td>48개월 점진 emission</td></tr><tr><td>Treasury / DAO</td><td>20%</td><td>200M</td><td>DAO 통제, 4년</td></tr><tr><td>Team &#x26; Advisors</td><td>18%</td><td>180M</td><td>12M cliff + 36M linear (총 48M)</td></tr><tr><td>Pre-Seed Investors</td><td>8%</td><td>80M</td><td>6M cliff + 18M linear, TGE 10%</td></tr><tr><td>Seed Strategic</td><td>7%</td><td>70M</td><td>3M cliff + 15M linear, TGE 10%</td></tr><tr><td>DEX/CEX Liquidity</td><td>7%</td><td>70M</td><td>TGE 50%, 12개월 내 50%</td></tr><tr><td>Protocol Liquidity Seeding</td><td>5%</td><td>50M</td><td>메인넷 마일스톤에 따라</td></tr></tbody></table>

총 공급의 50%가 community/ecosystem. 팀 vesting 48개월은 업계 표준 24개월보다 김.

### 2 7가지 토큰 유틸리티

### 2.1 Staking & 수익 공유

* **메커니즘:** PRX 스테이킹 → 프로토콜 거래 수수료의 50%를 USDC로 수령
* **Real yield:** 인플레이션 토큰이 아닌 실제 수수료 기반 수익. APY는 볼륨에 선형 비례
* **예시:** 월 $500K 볼륨 × 평균 1% 수수료 = 월 $5K USDC를 스테이커 풀에 분배
* **Activation:** P0 — TGE부터
* **Ref:** dYdX V4 (100% 수수료 → 스테이커), GMX (30% 수수료 → GLP 스테이커). PrediX는 스테이커·buyback-burn·treasury 간 균형을 위해 50/30/20 모델 선택

### 2.2 Oracle & 분쟁 해결

* **메커니즘:** PRX 스테이킹으로 commit-reveal 투표(해시 선공개, 이후 reveal)로 분쟁 결과 투표 — 프론트러닝 방지
* **Incentive:** 올바른 투표 → dispute bond 보상. 틀린 투표 → 비율로 슬래시
* **Escalation:** PRX 시총 10% 초과 분쟁 → 멀티시그로 에스컬레이션
* **Price markets:** Chainlink 피드로 자동 resolve (예: "BTC > $200K")
* **Activation:** P1 — 메인넷부터
* **Ref:** UMA DVM (Optimistic Oracle + bond 메커니즘). PrediX는 optimistic window 대신 commit-reveal로 확장

### 2.3 Gauge Voting (vePRX)

* **메커니즘:** PRX 락업 → vePRX 수령 (1–12개월, 오래 락업할수록 투표권 증가)
* **효과:** vePRX 홀더가 매주 어떤 마켓이 treasury LP 인센티브를 받을지 투표
* **Flywheel:** 표 많은 마켓 → 더 깊은 유동성 → 높은 볼륨 → 많은 수수료 → 스테이커 yield 증가 → PRX 수요 증가
* **차별점:** prediction market의 "Curve Wars" — 경쟁자 중 누구도 이 메커니즘을 보유하지 않음
* **Activation:** P0 — TGE부터
* **Ref:** Curve veCRV (CRV 락업 → gauge 투표 → $4B TVL 락업). Balancer veBAL. PrediX는 이 검증된 모델을 새 vertical에 적용

### 2.4 티어별 수수료 할인

* **메커니즘:** PRX 스테이킹 임계값 도달 → 거래 수수료 20–50% 할인
* **티어 유지 조건:** 월 최소 거래량과 결합
* **티어(예상):** Bronze (10% 할인), Silver (20%), Gold (35%), Diamond (50%)
* **Activation:** P0 — TGE부터
* **Ref:** Hyperliquid (볼륨 + 스테이킹 기반 fee tier). Binance BNB 수수료 할인. PrediX는 볼륨 단독이 아닌 스테이킹 + 볼륨 결합

### 2.5 Buyback-Burn

* **메커니즘:** 프로토콜 수익 30% → 공개 시장에서 PRX 자동 buyback → 영구 burn. 온체인 검증 가능
* **직접 연결:** 플랫폼 사용 증가 → burn rate 증가 → 공급 감소 → 자연스러운 price floor
* **추정:** 예상 규모에서 연간 공급의 1–3% burn
* **Activation:** P0 — TGE부터 (볼륨이 발생한 첫날부터)
* **Ref:** Hyperliquid (97% 수수료 → buyback = $49B FDV, 2025년 $716M burn). BNB 분기별 burn. PrediX는 스테이커 수익과 균형을 위해 97% 대신 30% 사용

### 2.6 Market 생성 Bond

* **메커니즘:** 10,000 PRX 스테이킹으로 허가 없이(permissionless) 마켓 생성
* **Incentive:** 유효한 resolve → bond 반환 + 보상 (마켓 수수료의 5–10%). 스팸/조작 → bond 전액 슬래시
* **효과:** 품질 필터 + 마켓 수 확장 시 PRX에 대한 구조적 수요 생성
* **Activation:** P1 — 메인넷 +3개월
* **Ref:** Azuro (pool creation bond). Hyperliquid HIP-3 (listing bond). PrediX는 market creator에게 bond + 수익 공유 결합

### 2.7 Collateral Yield Sharing

* **메커니즘:** vePRX 홀더가 idle USDC 담보에서 발생하는 yield를 분배받음 (프로토콜 USDC 70% → Aave v3 ~4% APY)
* **효과:** 거래 수수료 외 추가적인 real yield 소스, 볼륨이 아닌 TVL에 따라 확장
* **예시:** TVL $50M → 연간 ~$1.4M yield → vePRX 홀더에게 분배
* **Activation:** P2 — TGE +12개월
* **Ref:** GMX (GLP yield sharing — 수수료 70% 스테이커). Polymarket은 $416M USDC를 0% yield로 놀려 둠 — PrediX는 이를 수익원으로 전환

### 2.8 Activation Priority Roadmap

<table><thead><tr><th>Utility</th><th width="67">Priority</th><th width="135">Timeline</th><th>Impact</th><th>Benchmark</th></tr></thead><tbody><tr><td>Fee Buyback-Burn</td><td>P0</td><td>TGE부터</td><td>高 - price floor</td><td>Hyperliquid: 97% fee buyback</td></tr><tr><td>veToken Staking (Gauge)</td><td>P0</td><td>TGE부터</td><td>高 - 유통 공급 감소</td><td>Curve: veCRV 모델</td></tr><tr><td>LP Yield Boost</td><td>P0</td><td>TGE부터</td><td>高 - LP 유지</td><td>Curve/Balancer gauge</td></tr><tr><td>티어별 수수료 할인</td><td>P0</td><td>TGE부터</td><td>高 - 거래 인센티브</td><td>Hyperliquid fee tier</td></tr><tr><td>Market Creation Bond</td><td>P1</td><td>메인넷 +3M</td><td>中 - 수요 확장</td><td>Azuro: pool creation</td></tr><tr><td>Oracle Dispute Bond</td><td>P1</td><td>메인넷부터</td><td>中 - 시스템 안전</td><td>UMA: bond 메커니즘</td></tr><tr><td>NFT Pass Access</td><td>P2</td><td>TGE +6M</td><td>中 - 게임화</td><td>Blur: NFT + 토큰 모델</td></tr><tr><td>Collateral Yield Sharing</td><td>P2</td><td>TGE +12M</td><td>장기적으로 高</td><td>GMX: GLP yield sharing</td></tr></tbody></table>

### 3 수익 분배

<table><thead><tr><th>분배</th><th width="162">%</th><th>메커니즘</th></tr></thead><tbody><tr><td>PREDIX 스테이커</td><td>50%</td><td>USDC real yield</td></tr><tr><td>Buyback-Burn</td><td>30%</td><td>매수 → 영구 burn</td></tr><tr><td>Treasury</td><td>20%</td><td>운영 + gauge LP 인센티브</td></tr></tbody></table>

### 4 디플레이션 메커니즘

* **공급 감소:** Buyback-burn (연 1–3%), oracle slash, bond slash
* **공급 증가:** Oracle 보상 ~연 0.5%, 스테이킹 emission 거의 0
* **순 결과:** 월 볼륨 $2–4M 초과 시 디플레이션

### 5 TGE 조건

다음 조건을 동시 충족할 때까지 토큰 론칭 안 함: 월 볼륨 ≥$500K, 트레이더 ≥1,000, 활성 마켓 ≥10. TGE 전 Points 프로그램 운영.

### 6 매수 vs 매도 압력

<table><thead><tr><th>매수 압력</th><th width="114">영향</th><th>매도 리스크</th><th>완화</th></tr></thead><tbody><tr><td>Fee Buyback-Burn</td><td>高</td><td>Cliff 이후 팀 덤프</td><td>12M cliff + 36M linear</td></tr><tr><td>veToken Staking</td><td>高</td><td>LP 마이닝 emission</td><td>가우시안 점진 감소</td></tr><tr><td>Market Creation Bond</td><td>中</td><td>TGE 투자자 덤프</td><td>TGE 10%, 6M cliff</td></tr><tr><td>LP Yield Boost</td><td>中</td><td>초기 유틸리티 부족</td><td>Day 1부터 Fee buyback</td></tr><tr><td>Oracle Dispute Bond</td><td>低</td><td>—</td><td>—</td></tr></tbody></table>
