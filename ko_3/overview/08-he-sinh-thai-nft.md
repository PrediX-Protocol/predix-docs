# NFT 생태계

Unichain L2 민팅 비용: ~$0.01–$0.05/NFT. 게임화는 engagement 47%, loyalty 22% 증가 (Mintology 연구). 게임화나 NFT 레이어를 가진 prediction market 경쟁자는 없음. PrediX는 두 가지 기능 레이어의 NFT 생태계를 구축합니다: Identity/Gamification(리텐션 증가, 온체인 아이덴티티 생성)과 금융 자산(포지션 토큰화, 2차 시장 생성).

### 1 Identity & Gamification Layer

### Trader Passport — 트레이더 여권 (Soulbound, ERC-5192)

* **설명:** 가입 시 양도 불가한 Soulbound NFT를 각 사용자가 1개 민팅 — 온체인에 기록되는 "trader passport": 총 볼륨, 마켓 수, 승률, 최장 연승, 가입일
* **비주얼 진화:** Bronze → Silver → Gold → Diamond — 활동에 따라 자동 업그레이드
* **Utility:** Passport 레벨이 수수료 티어, 신규 마켓 조기 접근, 에어드롭 자격을 결정. Diamond Passport = 모든 프로토콜 이벤트에 우선 화이트리스트
* **왜 Soulbound:** 위조 불가, 매매 불가 — 진짜 온체인 아이덴티티
* **Ref:** Galxe Passport (DeFi 아이덴티티 레이어), Lens Protocol (소셜 그래프 NFT). PrediX는 구체적인 트레이딩 지표로 prediction market에 특화

### Achievement Badges — 업적 배지 (ERC-1155)

설명: 마일스톤 달성 시 자동 민팅되는 semi-fungible 배지. 배지 목록:

* First Prediction (Common) — 첫 거래
* Sniper (Uncommon) — 5연속 올바른 예측
* Whale (Rare) — 1개 마켓에서 $10K 이상
* Oracle (Epic) — 10회 이상 oracle 올바른 투표
* Season Champion (Legendary) — 시즌 리더보드 상위
* **Utility:** 배지 조합으로 혜택 잠금 해제:

         3 Rare → Pro Trader status (15% 수수료 할인 + 프로필에 배지 표시).

          5 Epic → Elite Trader (고급 분석 도구 접근)

* **Ref:** Blur 시즌 배지(게임화로 볼륨 유도), Rabbithole(퀘스트 보상). PrediX는 배지를 단순 치장이 아닌 수수료 할인에 직접 연결

### Season Competition & Trophy

* **설명:** 3개월 단위 시즌 — 볼륨, 승률, P\&L 기준 리더보드
* **Utility:** 상위 퍼포머는 Trophy NFT 획득: 20–30% 수수료 할인, PRX 에어드롭 보너스, 프리미엄 마켓 조기 접근, 다시는 민팅되지 않는 독점 배지
* **Ref:** 게이밍에서 검증된 Season Pass 모델 (Fortnite: 배틀패스로 $5.8B 수익). Hyperliquid Points Season. PrediX는 prediction market에 적용 — APAC 게임 문화에 적합

### Referral NFT — 추천 (Soulbound)

* **설명:** 성공 추천 수에 따라 진화
* **티어:** Connector (1–5 refs) → Ambassador (6–20) → Community Leader (21–50) → Network Builder (50+)
* **Utility:** Network Builder는 모든 추천 사용자의 거래 수수료 일정 %를 영구 수령. Ambassador+는 초대 전용 커뮤니티 및 신규 마켓 얼리 알파 접근
* **Ref:** Blur referral (영구 수수료 분배). Hyperliquid referral (5% 수수료). PrediX는 진화형 NFT + 수수료 분배 + CoinCraze 10만+ 네트워크를 결합

### 2 금융 자산 Layer

#### LP Position NFT (ERC-721)

* **설명:** prediction market 풀의 유동성 포지션을 표현
* **온체인 메타데이터:** 마켓 ID, 예치 USDC, 풀 share %, 누적 수수료, LP 범위
* **Utility:** 양도 가능 → remove liquidity 없이 LP 청산을 위한 2차 시장 생성. 향후 (Phase 3): 렌딩 프로토콜에서 담보로 사용해 USDC 대출
* **Ref:** Uniswap V3 LP NFTs (concentrated liquidity 포지션 토큰화). PrediX는 마켓별 메타데이터로 prediction market 풀에 확장

#### Limit Order Receipt NFT (ERC-721)

* **설명:** CLOB의 ERC-6909 limit order claim 래퍼
* **메타데이터:** 주문 유형(BUY/SELL), 토큰(YES/NO), 가격, 수량, 상태(active/filled/cancelled)
* **Utility:** 양도 가능 → limit order 포지션의 OTC 시장 생성. 트레이더는 cancel하는 대신 미체결 주문 권리를 판매 가능
* **Ref:** 직접적 선례 없음 — PrediX는 limit order를 tradeable NFT로 토큰화하는 최초의 프로토콜

#### Market Founder NFT (Phase 3, ERC-721)

* **설명:** 마켓 생성 시 민팅 (PREDIX bond 필요)
* **Utility:** Founder는 자신이 만든 마켓의 거래 수수료 5–10%를 영구 수령. Dynamic art가 씨앗 → 묘목 → 큰 나무로 볼륨에 따라 진화 — 마켓 성장을 시각적으로 표현
* **효과:** prediction market의 크리에이터 경제 촉진. Market creator를 장기 이해관계자로
* **Ref:** Mirror Writing NFTs (크리에이터 수익 공유). Azuro pool creator 인센티브. PrediX는 dynamic art + 영구 수수료 분배 추가

### 3 NFT Utility Summary

<table><thead><tr><th>NFT</th><th>표준</th><th width="128">양도</th><th>핵심 Utility</th><th>Ref</th></tr></thead><tbody><tr><td>Trader Passport</td><td>ERC-5192</td><td>불가 (Soulbound)</td><td>수수료 티어, 화이트리스트, 자격</td><td>Galxe, Lens</td></tr><tr><td>Achievement Badge</td><td>ERC-1155</td><td>가능</td><td>조합 혜택, Pro/Elite 지위</td><td>Blur, Rabbithole</td></tr><tr><td>Season Trophy</td><td>ERC-721</td><td>가능</td><td>수수료 할인, 에어드롭 보너스</td><td>Fortnite Pass, Hyperliquid</td></tr><tr><td>Referral NFT</td><td>ERC-5192</td><td>불가 (Soulbound)</td><td>영구 수수료 분배, 얼리 액세스</td><td>Blur, Hyperliquid ref</td></tr><tr><td>LP Position</td><td>ERC-721</td><td>가능</td><td>LP 2차 시장, 담보</td><td>Uniswap V3 LP NFT</td></tr><tr><td>Limit Order Receipt</td><td>ERC-721</td><td>가능</td><td>limit order OTC 시장</td><td>선례 없음</td></tr><tr><td>Market Founder</td><td>ERC-721</td><td>가능</td><td>영구 수수료 5–10%, dynamic art</td><td>Mirror, Azuro</td></tr></tbody></table>
