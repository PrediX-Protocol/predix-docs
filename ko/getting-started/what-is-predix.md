---
description: PrediX Protocol 아키텍처 및 핵심 차별화 요소 개요
---

# PrediX란?

PrediX Protocol은 Uniswap v4 위에 구축되고 Unichain L2에 배포된 **탈중앙화 예측 시장 플랫폼**입니다. 누구나 실제 세상의 이벤트에 대한 마켓을 생성하고, 완전히 트러스트리스한 온체인 환경에서 결과 토큰을 거래할 수 있습니다.

## 핵심 차별화 요소

| 기능 | PrediX | 기타 |
| ------- | ------ | ------ |
| **거래 엔진** | 하이브리드 CLOB + AMM + Smart Router | CLOB만 (Polymarket) 또는 AMM만 (Augur) |
| **결과 토큰** | ERC-20 (모든 DeFi와 조합 가능) | ERC-1155 (제한적 조합성) |
| **인프라** | Uniswap v4 Hooks + Flash Accounting | 커스텀 또는 레거시 AMM |
| **업그레이드** | Diamond Proxy (EIP-2535) | 재배포 또는 업그레이드 불가 |
| **오라클 시스템** | 플러거블 (Manual, Chainlink, 확장 가능) | 단일 오라클, 고정적 |
| **LP 보호** | 동적 수수료: 만기 근처 0.5% → 5% | 보호 없음 |

## 아키텍처 개요

```
                         User
                          │
              ┌───────────┼───────────┐
              ▼           ▼           ▼
         PrediXRouter  PrediXExchange  Diamond Proxy
         (Smart Route)  (CLOB)        (Market Management)
              │           │           │
              │     ┌─────┘     ┌─────┘
              ▼     ▼           ▼
         Uniswap v4        MarketFacet ─── OutcomeTokens (ERC-20)
         PoolManager       AccessControl
              │            OwnableFacet
              ▼            PausableFacet
         PrediXHookV1
         (Dynamic Fees,       Oracle Adapters
          Anti-Sandwich)      ├── ManualOracle
                              ├── ChainlinkAdapter
                              └── [Future: UMA, API3]
```

### 핵심 컨트랙트

| 컨트랙트 | 역할 |
| -------- | ---- |
| **Diamond** | 마켓 관리: 생성, 분할, 병합, 해결, 수령 |
| **Exchange** | 99-tick 비트맵 매칭 엔진을 갖춘 온체인 CLOB |
| **Router** | 최적 체결을 위한 CLOB + AMM 스마트 라우팅 |
| **Hook** | Uniswap v4 Hook: 동적 수수료, 샌드위치 방지, 풀 등록 |
| **Oracle** | 플러거블 해결 시스템 (Manual, Chainlink, 확장 가능) |

---

**다음**: [작동 방식](how-it-works.md) · [빠른 시작](quick-start.md) · [핵심 개념](../concepts/prediction-markets.md)
