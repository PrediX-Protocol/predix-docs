---
description: PrediX Protocol에 대한 자주 묻는 질문
---

# FAQ

## PrediX란 무엇인가요?

PrediX Protocol은 Uniswap v4 (Unichain L2) 위에 구축된 탈중앙화 예측 시장 플랫폼입니다. 온체인 CLOB과 AMM을 결합하여 최적의 거래 경험을 제공합니다.

## 거래를 어떻게 시작하나요?

1. Unichain Sepolia에 지갑을 연결
2. Faucet에서 테스트 USDC를 받기
3. Router 컨트랙트에 USDC 승인
4. Router를 통해 `buyYes` 또는 `buyNo` 호출

자세한 내용은 [빠른 시작](../getting-started/quick-start.md) 가이드를 참조하세요.

## 마켓이 해결되면 어떻게 되나요?

오라클이 결과를 보고한 후, 누구나 `resolveMarket`을 호출할 수 있습니다. 승리 토큰 보유자는 토큰당 $1.00를 수령합니다. 패배 토큰은 가치가 없어집니다.

## 투자한 것 이상을 잃을 수 있나요?

**아닙니다.** 최대 손실은 결과 토큰에 지불한 금액입니다. 레버리지, 마진, 청산이 없습니다.

## 가격은 어떻게 결정되나요?

가격은 AMM 풀과 CLOB 호가창의 수요와 공급에 의해 결정됩니다. Smart Router가 두 소스를 모두 집합합니다. 가격은 자연스럽게 마켓의 집합적 확률 추정을 반영합니다.

## Split/Merge란 무엇인가요?

- **Split**: 1 USDC 예치 → 1 YES + 1 NO 토큰 수령
- **Merge**: 1 YES + 1 NO 소각 → 1 USDC 수령

이 메커니즘이 YES + NO ≈ $1.00으로 토큰 가격을 고정합니다.

## 수수료는 얼마인가요?

AMM 수수료는 0.5% (만기 7일 이상 전)에서 5% (24시간 미만)까지 범위입니다. CLOB 수수료는 MINT/MERGE 잉여금에서 발생합니다. [수수료](../concepts/fees.md)를 참조하세요.

## PrediX는 감사를 받았나요?

네. 264개 테스트, 14개 퍼즈 테스트 (각 10K 실행), 31개 온체인 E2E 테스트, 3개의 독립 감사에서 22개 버그 발견 및 수정, OWASP SC Top 10 2026 10/10 커버리지. [감사 보고서](../security/audit-reports.md)를 참조하세요.

## PrediX는 어떤 체인에 있나요?

Unichain Sepolia (테스트넷). Unichain 메인넷 배포는 2026년 Q3에 계획되어 있습니다.

## 테스트 USDC는 어떻게 받나요?

PrediX 테스트넷 faucet을 방문하거나 Unichain Sepolia의 `0x12fd156C8b5F2901BA2781d97db84AaC56b2b911` 주소에서 직접 민트하세요.

---

**다음**: [용어집](glossary.md) · [빠른 시작](../getting-started/quick-start.md)
