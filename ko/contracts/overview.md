---
description: 스마트 컨트랙트 아키텍처 및 배포 주소
---

# 컨트랙트 개요

## 아키텍처

PrediX Protocol은 5개의 핵심 컨트랙트로 구성됩니다:

| 컨트랙트 | 주소 (Unichain Sepolia) | 역할 |
| -------- | -------------------------- | ---- |
| **Diamond** | `0xF38a265E6e4F57D000a1CC08004da5B4A380B08A` | 마켓 관리 (EIP-2535 프록시) |
| **Hook** | `0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0` | Uniswap v4 Hook: 동적 수수료 + 샌드위치 공격 방지 |
| **Exchange** | `0xa202abCb2A358c0862B2dA76b553398339F2C638` | 온체인 CLOB 오더북 |
| **Router** | `0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7` | 스마트 라우팅: CLOB + AMM 통합 |
| **Oracle** | `0x699A8C74663b1C852E195b2ffa00D5965E992Cf3` | ManualOracleAdapter |

### 외부 의존성

| 컨트랙트 | 주소 |
| -------- | ------- |
| USDC | `0x12fd156C8b5F2901BA2781d97db84AaC56b2b911` |
| Uniswap v4 PoolManager | `0x00B036B58a818B1BC34d502D3fE730Db729e62AC` |

## Diamond Proxy (EIP-2535)를 사용하는 이유

- **24KB 제한 우회**: 복잡한 프로토콜 로직이 EVM 컨트랙트 크기 제한을 초과
- **세분화된 업그레이드**: 전체 재배포 없이 개별 함수 교체 가능
- **공유 스토리지**: 모든 Facet이 동일한 스토리지 레이아웃을 공유
- **프로덕션 검증 완료**: Aavegotchi, Beanstalk 등에서 사용

## 체인 정보

| 파라미터 | 값 |
| --------- | ----- |
| 네트워크 | Unichain Sepolia (테스트넷) |
| 체인 ID | `1301` |
| RPC | `https://sepolia.unichain.org` |
| 익스플로러 | `https://sepolia.uniscan.xyz` |

---

**다음**: [Diamond 컨트랙트](diamond.md) · [Exchange](exchange.md) · [Router](router.md) · [Hook](hook.md) · [Oracle](oracle.md)
