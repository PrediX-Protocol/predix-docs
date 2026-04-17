# 제품 개요

### 1 프로토콜 아키텍처

PrediX는 Unichain(OP Stack L2) 위에서 작동하는 완전 온체인 prediction market입니다. 비수탁형 — 사용자가 항상 프라이빗 키를 보유합니다. 5개 핵심 스마트 컨트랙트 모듈로 구성:

* **Diamond Proxy (EIP-2535):** 6개 facet — Cut, Loupe, AccessControl, Ownable, Pausable, Market. 재배포 없이 모듈 업그레이드
* **Exchange (CLOB):** 99-tick 비트맵 온체인 오더북, $0.01 단위로 $0.01–$0.99 limit order
* **Router (Smart Router):** AMM + CLOB 통합, 최적 라우팅, 다단계 원자적 flash accounting
* **Hook (Uniswap v4):** 시간 감쇠 dynamic fee, 샌드위치 방어, buyNo/sellNo 원자 실행
* **Oracle (다중 오라클):** ManualOracleAdapter, ChainlinkAdapter, UMACrossChainAdapter

### 2 왜 Unichain인가

Polygon PoS, Arbitrum, Base, Solana, Unichain을 평가했습니다. Unichain을 선택한 이유:

* 200ms Flashblocks (Polygon 2s 대비 10배 빠름)
* Uniswap v4 네이티브 (별도 배포 불필요)
* Revert protection (실패 거래 = $0 가스)
* TEE ordering으로 MEV 감소
* Circle CCTP V2 통한 USDC 네이티브

### 3 ERC-20 Outcome Tokens

가장 중요한 아키텍처 결정입니다. Polymarket은 ERC-1155(Gnosis CTF)를 사용 → DEX에서 LP 불가, 담보 사용 불가, 지갑에 hex hash로 표시. PrediX의 ERC-20:

* v4에서 YES/USDC LP로 prediction 포지션 유지하며 swap fee 획득
* 렌딩 프로토콜 담보로 사용 (Phase 3)
* Vault 전략 및 구조화 상품
* USDC와 맞춘 6 decimal. 불변식: YES supply = NO supply = totalCollateral

### 4 Diamond Pattern (EIP-2535)

48개 소스 파일과 6개 facet은 24KB 단일 컨트랙트에 들어가지 않습니다. Facet별 개별 업그레이드, 공유 스토리지로 가스 절감.

### 5 Protocol-Managed Liquidity

createMarket()이 각 마켓에 $5–30K를 자동 시딩합니다. Chainlink Automation이 리밸런싱. Resolve 시 시드 자본의 ~85–95%를 회수하는 회전 구조.

### 6 크로스체인 (CCTP V2 + Hyperlane)

Circle CCTP V2로 USDC 입출금 (네이티브 mint/burn, 30초 이내). Hyperlane으로 크로스체인 오라클. 단일 UX 플로우에 Deposit + trade.

### 7 사용자 플로우 (Step-by-Step)

* 지갑 연결 → Unichain에서 MetaMask/WalletConnect
* USDC 입금 → 직접 또는 다른 체인에서 CCTP V2로
* 마켓 선택 → 카테고리로 브라우징 (Sports, Crypto, Politics…)
* YES 또는 NO 매수 → Router가 CLOB/AMM 사이에서 최적 라우팅
* 결과 대기 → 만료 후 Oracle이 resolve
* Redeem → 올바른 토큰 1:1 USDC 교환, 틀린 토큰 = $0

### 8 컨트랙트 주소 (Unichain Sepolia Testnet)

<table><thead><tr><th width="182.1640625">Contract</th><th>주소</th></tr></thead><tbody><tr><td>Diamond</td><td>0xF38a265E6e4F57D000a1CC08004da5B4A380B08A</td></tr><tr><td>Hook</td><td>0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0</td></tr><tr><td>Exchange</td><td>0xa202abCb2A358c0862B2dA76b553398339F2C638</td></tr><tr><td>Router</td><td>0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7</td></tr><tr><td>Oracle</td><td>0x699A8C74663b1C852E195b2ffa00D5965E992Cf3</td></tr><tr><td>USDC</td><td>0x12fd156C8b5F2901BA2781d97db84AaC56b2b911</td></tr><tr><td>Pool Manager</td><td>0x00B036B58a818B1BC34d502D3fE730Db729e62AC</td></tr></tbody></table>

Chain ID: 1301 (Unichain Sepolia)
