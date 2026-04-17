# References & Benchmarks

PrediX는 바퀴를 재발명하지 않습니다 — 이 프로토콜은 가장 성공한 DeFi 프로젝트들에서 검증된 메커니즘을 결합해 prediction market vertical에 적용합니다. 아래는 전체 레퍼런스 표:

### 1 Token Mechanics References

| PrediX 메커니즘                   | 참조 프로토콜                                      | 적용 방법                                           | 원 프로토콜 결과                        |
| ----------------------------- | -------------------------------------------- | ---------------------------------------------- | ------------------------------- |
| Fee Buyback-Burn (수익 30%)     | Hyperliquid (fee 97% → buyback)              | 비율 축소, 스테이커 yield와 균형                          | $49B FDV, 2025년 $716M burn      |
| veToken Staking + Gauge Voting | Curve Finance (veCRV)                        | prediction market에 gauge voting 최초 적용          | $4B TVL locked, "Curve Wars" 생태계 |
| LP Yield Boost (1.5x–2.5x)    | Curve/Balancer (veBAL boost)                 | 장기 락업 + 유동성 공급 인센티브                            | Balancer: BAL 80%+ 락업           |
| Real Yield Staking (fee 50%)  | dYdX V4 (fee 100% → 스테이커), GMX (30% → GLP)  | 균형잡힌 50/30/20 모델                               | dYdX: 일 볼륨 $1B+. GMX: TVL $150M |
| Oracle Dispute Bond           | UMA (Optimistic Oracle + DVM)                | optimistic window 대신 commit-reveal             | UMA: dispute resolution 99.9% 정확  |
| Market Creation Bond          | Azuro (pool creation), Hyperliquid HIP-3     | bond + 수익 분배 결합                                | Azuro: 100+ 데이터 제공자             |
| Collateral Yield              | GMX (GLP yield sharing)                      | vePRX 홀더가 idle USDC yield 분배                   | GMX: ETH fee 70% → 스테이커         |
| Fee Tiers by Staking          | Hyperliquid, BNB (Binance)                   | 스테이킹 + 볼륨 임계값 결합                               | Binance: BNB 할인이 핵심 동인         |

### 2 NFT & Gamification References

| PrediX 메커니즘                         | 참조 프로토콜                               | 적용 방법                                             | 원 결과                          |
| ---------------------------------- | ------------------------------------- | ------------------------------------------------ | ----------------------------- |
| Trader Passport (Soulbound)        | Galxe Passport, Lens Protocol         | PM 트레이더 전용 아이덴티티 레이어                             | Galxe: 1,500만+ 패스포트          |
| Achievement Badges → Fee 할인       | Blur Season badges, Rabbithole        | cosmetic 대신 실질 utility(수수료)에 배지 직접 연결           | Blur: NFT 시장 점유율 80%         |
| Season Competition                 | Fortnite Battle Pass, Hyperliquid Points | prediction market에 Season Pass 모델                | Fortnite: 배틀패스로 $5.8B         |
| Referral NFT → Fee Sharing         | Blur referral, Hyperliquid referral   | 진화형 NFT + 영구 수수료 분배                              | Hyperliquid: 5% referral 프로그램 |
| LP Position NFT                    | Uniswap V3 LP NFTs                    | prediction market LP 토큰화, 2차 시장 가능              | Uniswap V3: LP NFT로 TVL $5B+  |
| Market Founder NFT                 | Mirror Writing NFTs, Azuro pool creator | 크리에이터에게 dynamic art + 영구 수익 분배                  | Mirror: 온체인 크리에이터 경제          |

### 3 Architecture References

| PrediX 구성 요소            | 참조 프로토콜                           | 선정 이유                                     |
| ------------------------ | --------------------------------- | ----------------------------------------- |
| Hybrid AMM+CLOB          | Hyperliquid (CLOB), Uniswap (AMM) | 양쪽 장점 결합 — PM에서 누구도 구현 못함              |
| ERC-20 Outcome Tokens    | Azuro (ERC-20) vs Polymarket (ERC-1155) | DeFi composable — LP, lending, vault      |
| Diamond Proxy (EIP-2535) | Aavegotchi, Louper                | 모듈 업그레이드, 24KB 제한 극복                     |
| Uniswap v4 Hooks         | Uniswap v4 생태계                    | Dynamic fee, anti-sandwich, pool registration |
| CCTP V2 Cross-chain      | Circle CCTP                       | 네이티브 USDC mint/burn, 30초 이내              |
| Protocol-Managed LP      | Olympus DAO (protocol-owned liquidity) | 마켓당 $5–30K 자동 시드, cold-start 해결          |

### 4 Fundraising Comparables

<table><thead><tr><th width="136">Protocol</th><th width="123">Raise</th><th width="118">Raise 당시 FDV</th><th width="153">현재</th><th>PrediX 교훈</th></tr></thead><tbody><tr><td>Polymarket</td><td>$4M seed (2020)</td><td>$25M</td><td>~$20B 밸류에이션</td><td>볼륨 = 해자. 그러나 33개국+ 금지</td></tr><tr><td>Kalshi</td><td>Tier-1 VC</td><td>~$100M</td><td>$22B, 연 수익 $1.5B</td><td>CFTC 라이선스 = 독점. 그러나 월 운영비 $500K+</td></tr><tr><td>Opinion (OPN)</td><td>$25M total</td><td>~$50M seed</td><td>TGE $450M FDV</td><td>Binance Labs = 신뢰성. 그러나 D1 17.8% 덤프</td></tr><tr><td>PredictEX</td><td>$10M</td><td>~$30–50M</td><td>Private beta</td><td>Exchange VC = 배포. Exchange VC 1곳 = 마케팅 $5M</td></tr><tr><td>Azuro</td><td>$11M Series A</td><td>~$30M</td><td>Multi-chain</td><td>인프라 플레이. 그러나 토큰 utility 약함</td></tr><tr><td>Novig</td><td>$18M Series A</td><td>~$80M+</td><td>Sports P2P PM</td><td>스포츠 PM vertical 검증. 미국 집중</td></tr><tr><td>PrediX</td><td>$1.5–2M</td><td>$15–20M</td><td>컨트랙트 완료</td><td>카테고리 최저 FDV + 최저 손익분기</td></tr></tbody></table>
