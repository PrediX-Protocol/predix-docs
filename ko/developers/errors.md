---
description: 일반적인 오류 및 해결 방법
---

# 오류 참조

## 일반적인 오류

| 오류 | 원인 | 해결 방법 |
| ----- | ----- | -------- |
| `Market_NotFound` | 유효하지 않은 marketId | MarketCreated 이벤트에서 marketId 확인 |
| `Market_Ended` | 마켓의 endTime이 경과함 | 만료 후에는 거래 불가 |
| `Market_ExceedsPerTradeCap` | 거래당 안전 한도 초과 | 거래 크기 줄이기 |
| `Market_ExceedsTvlCap` | 전체 시스템 TVL 한도 도달 | 대기하거나 소액 사용 |
| `Market_ExceedsPerMarketCap` | 마켓의 담보금 한도 도달 | 소액 사용 |
| `InvalidPrice` | 가격이 $0.01 틱 그리드에 맞지 않음 | `10000` (6 소수점)의 배수 사용 |
| `MaxOrdersExceeded` | 이 마켓에서 사용자의 주문이 50개 | 기존 주문을 먼저 취소 |
| `MarketNotActive` | 마켓 만료, 정산됨 또는 환불 모드 | 마켓 상태 확인 |
| `InsufficientBalance` | 작업에 필요한 토큰 부족 | 토큰 잔액 확인 |
| `SandwichDetected` | 같은 블록에서 반대 방향 스왑 | 다음 블록까지 대기 |
| `SlippageExceeded` | 출력이 minOut 미만 | 슬리피지 높이거나 재시도 |
| `DeadlineExpired` | 데드라인 이후에 트랜잭션 채굴됨 | 더 긴 데드라인 설정 |
| `OnlyFactory` | 토큰의 직접 민트/소각 시도 | 토큰은 Diamond를 통해서만 민트 가능 |

## 디버깅 팁

```typescript
try {
  await router.buyYes(marketId, usdcIn, minYesOut, recipient, deadline);
} catch (error: any) {
  // 커스텀 오류 디코딩
  const iface = new ethers.Interface(ROUTER_ABI);
  try {
    const decoded = iface.parseError(error.data);
    console.error("커스텀 오류:", decoded?.name, decoded?.args);
  } catch {
    console.error("원시 오류:", error.message);
  }
}
```

---

**다음**: [개발자 빠른 시작](quickstart.md) · [거래 연동](trading-integration.md)
