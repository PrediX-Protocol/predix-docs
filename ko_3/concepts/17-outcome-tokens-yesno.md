# 17. Outcome Tokens (YES/NO)

### 기술 사양

* **표준:** ERC-20 + ERC-2612 Permit (gasless approval)
* **Decimals:** 6 (USDC와 동일)
* **Mint/Burn:** Diamond 컨트랙트만 권한 보유 (onlyFactory)
* **공급 불변식:** YES supply = NO supply = totalCollateral

### 왜 ERC-20인가?

Polymarket은 ERC-1155 (Gnosis CTF) 사용 → LP 불가, 담보 불가, 지갑에 hex로 표시. PrediX의 ERC-20은 전체 DeFi에 네이티브 통합: DEX에서 LP, 렌딩 담보, vault 전략.

### 토큰 수명주기

* **Mint:** Split $1 USDC → 1 YES + 1 NO
* **Trade:** CLOB 또는 AMM에서 매매
* **LP:** YES/USDC 유동성 공급해 수수료 획득
* **Redeem:** Resolve 후 올바른 토큰을 1:1 USDC로 교환
