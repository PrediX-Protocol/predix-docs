# 38. Common Errors

| Error | 원인 | 해결 |
|---|---|---|
| Market_NotFound | marketId 존재하지 않음 | marketId 재확인 |
| Market_Ended | endTime 경과 | 만료된 market은 거래 불가 |
| Market_ExceedsPerTradeCap | Amount 과대 | 거래를 분할 |
| InvalidPrice | 유효하지 않은 가격 | $0.01–$0.99, 단위 $0.01 |
| MaxOrdersExceeded | market당 50 주문 도달 | 이전 주문 취소 |
| MarketNotActive | market expired/resolved/refund | market 상태 확인 |
| InsufficientBalance | 토큰 부족 | 잔액 확인 |
| SlippageExceeded | 가격 변동 과다 | 슬리피지 허용치 증가 |
