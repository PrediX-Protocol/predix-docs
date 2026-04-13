---
description: 주요 용어와 정의
---

# 용어집

| 용어 | 정의 |
| ---- | ---------- |
| **AMM** | 자동화된 마켓 메이커 — 알고리즘 거래 풀 (Uniswap v4) |
| **CLOB** | 중앙 지정가 주문장 — 전통적인 주문 매칭 엔진 |
| **Collateral (담보)** | 결과 토큰의 지원으로 예치된 USDC |
| **Concentrated Liquidity** | 자본 효율을 위해 특정 가격 범위에 집중된 LP 포지션 |
| **Diamond Proxy** | EIP-2535 업그레이드 패턴 — 단일 프록시, 다수의 구현 컨트랙트 (facets) |
| **Dynamic Fees (동적 수수료)** | 만기가 다가올수록 0.5%에서 5%로 증가하는 AMM 수수료 |
| **ERC-20** | Ethereum 토큰 표준 — 대체 가능, DeFi와 조합 가능 |
| **ERC-2612** | 서명을 통한 가스리스 토큰 승인 Permit 확장 |
| **Facet** | Diamond 프록시 뒤의 구현 컨트랙트 |
| **Hook** | Uniswap v4 플러그인으로 풀 운영에 커스텀 로직 추가 |
| **Merge (병합)** | 1 YES + 1 NO를 소각하여 1 USDC 수령 |
| **NO 토큰** | 이벤트가 발생하지 않으면 $1을 지급하는 ERC-20 토큰 |
| **Oracle** | 이벤트 결과를 보고하는 외부 데이터 소스 |
| **Outcome Token (결과 토큰)** | 예측 시장에서 포지션을 나타내는 YES 또는 NO 토큰 |
| **Prediction Market (예측 시장)** | 미래 이벤트의 결과에 대해 거래하는 마켓플레이스 |
| **Redeem (수령)** | 마켓 해결 후 승리 토큰을 USDC로 교환 |
| **Slippage (슬리피지)** | 예상 거래 가격과 실행된 거래 가격의 차이 |
| **Smart Router** | CLOB과 AMM 유동성을 집합하는 PrediX Router |
| **Split (분할)** | 1 USDC를 예치하여 1 YES + 1 NO 토큰 수령 |
| **Spread (스프레드)** | 최우선 매수가와 최우선 매도가의 차이 |
| **TVL** | 총 잠긴 가치 — 시스템에 예치된 총 USDC |
| **USDC** | USD Coin — 담보로 사용되는 스테이블코인 (6 decimals) |
| **YES 토큰** | 이벤트가 발생하면 $1을 지급하는 ERC-20 토큰 |

---

**다음**: [FAQ](faq.md) · [컨트랙트 주소](contract-addresses.md)
