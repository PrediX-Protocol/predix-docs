# Errors

Custom error trả về bởi các contract PrediX. Tất cả đều là Solidity custom error (tiết kiệm gas, không dùng string message).

## Lỗi thường gặp

| Error | Contract | Nguyên nhân | Khắc phục |
|-------|----------|-------|-----|
| `Market_NotFound` | Diamond | `marketId` không hợp lệ | Check `isMarketExists(marketId)` |
| `Market_Ended` | Diamond/Exchange | Market đã qua `endTime` | Check `endTime` trước khi trade |
| `Market_NotEnded` | Diamond | Resolve khi chưa đến `endTime` | Chờ đến `endTime` |
| `Market_AlreadyResolved` | Diamond | Market đã resolve rồi | Check `isResolved` trong MarketData |
| `Market_NotResolved` | Diamond | Redeem trước khi resolve | Chờ `resolveMarket()` |
| `Market_ExceedsPerTradeCap` | Diamond | Lệnh quá lớn | Giảm amount hoặc check `getSafetyCaps()` |
| `Market_ExceedsTvlCap` | Diamond | Đã chạm TVL cap | Chờ TVL giảm |
| `Market_ExceedsPerMarketCap` | Diamond | Chạm cap collateral của market | Giảm amount |
| `InvalidPrice` | Exchange | Giá không đúng tick $0.01 (10000–990000) | Dùng giá là bội của 10000 |
| `MaxOrdersExceeded` | Exchange | Vượt giới hạn 50 lệnh/user/market | Huỷ bớt lệnh cũ |
| `MarketNotActive` | Exchange | Market hết hạn, đã resolve, hoặc refund mode | Kiểm tra trạng thái |
| `InsufficientBalance` | ERC-20 | Không đủ token | Check `balanceOf` trước |
| `InsufficientAllowance` | ERC-20 | Chưa approve | Gọi `approve(spender, amount)` |
| `DeadlineExpired` | Router | Giao dịch quá chậm | Tăng deadline hoặc retry |
| `SlippageExceeded` | Router | Output thấp hơn `minOut` | Tăng dung sai slippage |
| `OracleNotApproved` | Diamond | Oracle chưa được whitelist | Admin gọi `setApprovedOracle` |
| `RefundMode_Active` | Diamond | Market đang refund mode | Dùng `refund()` |
| `NotOperator` | Diamond | Caller thiếu OPERATOR_ROLE | Dùng signer đúng role |
| `EmergencyNotReady` | Diamond | Chưa đủ 7d từ endTime | Chờ đủ 7 ngày |

## Pattern xử lý lỗi

```typescript
try {
  const tx = await router.buyYes(marketId, usdcIn, minOut, recipient, deadline);
  await tx.wait();
} catch (error: any) {
  const reason = error.reason || error.message;

  // Map sang message thân thiện
  const errorMap: Record<string, string> = {
    "Market_NotFound": "Market này không tồn tại.",
    "Market_Ended": "Market đã hết hạn. Ngừng giao dịch.",
    "SlippageExceeded": "Giá biến động quá nhiều. Tăng slippage rồi thử lại.",
    "DeadlineExpired": "Giao dịch quá lâu. Vui lòng thử lại.",
    "Market_ExceedsPerTradeCap": "Lệnh quá lớn. Thử số nhỏ hơn.",
  };

  for (const [key, msg] of Object.entries(errorMap)) {
    if (reason.includes(key)) {
      console.error(msg);
      return;
    }
  }
  console.error("Unknown error:", reason);
}
```

## Tiếp theo

- [Tích hợp giao dịch](trading-integration.md) — hướng dẫn tích hợp đầy đủ
- [Events](events.md) — event hỗ trợ debug
- [Bảo mật](../contracts/security.md) — cap và giới hạn an toàn
