# 13. 리스크 & 완화책

<table><thead><tr><th width="224">리스크</th><th width="129">수준</th><th>완화</th></tr></thead><tbody><tr><td>유동성 Cold-Start</td><td>高</td><td>마켓당 Protocol LP $5–30K, CoinCraze 10만+, Chainlink 리밸런싱</td></tr><tr><td>스마트 컨트랙트 취약점</td><td>高</td><td>264 tests, 3회 AI 감사, Slither 0 이슈, Fuzz 14×10K, 31 E2E 온체인</td></tr><tr><td>법적 리스크</td><td>高</td><td>비수탁형, US/SG geo-block, BVI 재단</td></tr><tr><td>오라클 조작</td><td>中</td><td>다중 오라클, commit-reveal, slash, 멀티시그 에스컬레이션</td></tr><tr><td>토큰 가격 하락</td><td>中</td><td>50% buyback-burn, USDC real yield, PMF 이후 TGE</td></tr><tr><td>Polymarket 경쟁</td><td>中</td><td>APAC 집중, ERC-20 vs ERC-1155, gauge voting, NFT 게임화</td></tr><tr><td>낮은 볼륨</td><td>中</td><td>FIFA WC 촉매, APAC 문화, $4.5–7M 손익분기 달성 가능</td></tr><tr><td>팀 실행</td><td>低</td><td>컨트랙트 배포 완료, 264 tests, SP Labs 40% 공동 자금</td></tr></tbody></table>

## 보안 아키텍처

* **Safety Caps:** TVL, 거래당, 마켓당 (설정 가능)
* **Pause Module:** 개별 facet 또는 전체 일시 정지
* **Emergency Resolve:** endTime 이후 7일 지연
* **Refund Mode:** 비율대로 USDC 환불
* **Anti-Sandwich:** 200ms Flashblocks + TEE ordering
* **Reentrancy Guards:** 3종 보호
