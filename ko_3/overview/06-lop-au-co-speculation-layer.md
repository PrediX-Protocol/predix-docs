# Speculation Layer

### 1 Outcome Token으로 유동성 최적화

PrediX의 outcome token은 ERC-20 표준을 따르므로, 보유자가 Uniswap v4의 풀(예: YES/USDC)에 유동성을 공급해 예측 포지션을 유지하면서 swap 수수료를 받을 수 있습니다.

다시 말해, outcome token을 지갑에 묶어두고 이벤트 종료를 기다리는 대신, DeFi 생태계 안에서 실제로 작동시킬 수 있습니다. 토큰 아키텍처의 한계로 다른 주요 prediction 플랫폼은 구현하지 못한 기능입니다.

***

### 2 Composability — 확장 로드맵 (Phase 3)

outcome token이 표준 ERC-20이고 Unichain(EVM 호환)에서 작동하므로, PrediX는 향후 다른 DeFi 프로토콜과 통합할 수 있습니다. 연구 중인 방향:

**Lending & Collateral** : 렌딩 프로토콜에서 outcome token(YES/NO)을 담보로 사용 — 자본 관리의 유연성을 엶.

**전략 Vault:** 다양한 예측 포지션을 결합해 단일 진입점으로 여러 시장에 노출될 수 있는 vault.

**레버리지 포지션** : 보유 중인 outcome token을 기반으로 대출해 참여 규모를 확장. 청산 리스크가 높아져 경험 많은 사용자에게만 적합.

**AI Agent SDK** — 개발자가 예측 시장과 상호작용하는 자동 에이전트를 만들 수 있는 툴킷: 전략 실행, 백테스팅, 예측 모델 마켓플레이스.

> **Disclaimer:** Phase 3 기능은 연구·개발 단계이며 출시 일정이 확정되지 않았습니다. 최종 아키텍처는 각 시장의 기술적 조건 및 규제에 따라 변경될 수 있습니다.

#### 6.1 예측 포지션에서 수익 창출

일반적으로 prediction market에서 YES 또는 NO 토큰을 사면, 해당 자본은 "잠자고" 있습니다 — 이벤트 종료 시에만 승패가 결정되죠. 그게 전부입니다.

**PrediX는 이를 바꿉니다.**

YES/NO outcome token이 표준 ERC-20이기 때문에, YES 토큰을 Uniswap v4의 YES/USDC 유동성 풀에 넣을 수 있습니다 — 즉, **예측 베팅을 그대로 유지**하면서 풀의 거래자로부터 **swap 수수료를 획득**합니다.

> 간단히: 결과를 기다리는 동안 자본이 더 이상 "잠자지" 않고, 매일 수익을 냅니다.

이는 주요 prediction 플랫폼(Polymarket, Kalshi 등) 어느 곳도 구현하지 못한 부분입니다 — 그들은 표준 ERC-20 outcome token을 갖춘 AMM 위에 구축되지 않았기 때문입니다.

***

#### 6.2 미래 확장 가능성 (Phase 3)

플랫폼이 성숙하면, PrediX는 더 복잡한 금융 레이어를 열어갑니다:

<table><thead><tr><th width="274">기능</th><th>실질적 의미</th></tr></thead><tbody><tr><td><strong>Outcome Token 렌딩</strong></td><td>YES/NO 토큰을 담보로 USDC 대출 — 베팅을 매도하지 않고 유동성 확보</td></tr><tr><td><strong>Structured Vault</strong></td><td>여러 예측 포지션을 자동 결합해 패키지화된 투자 상품</td></tr><tr><td><strong>레버리지 예측</strong></td><td>현재 포지션을 기반으로 추가 자본 대출해 수익(및 리스크) 확장</td></tr><tr><td><strong>AI Agent SDK</strong></td><td>자동 트레이딩 봇, 전략 마켓플레이스, 엔진을 위한 툴킷</td></tr></tbody></table>
