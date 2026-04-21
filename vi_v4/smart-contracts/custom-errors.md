---
description: Registry của mọi custom error trong PrediX contracts — lookup khi debug revert.
---

# Custom Errors Registry

PrediX dùng custom errors (Solidity 0.8.4+) thay vì `require(string)` — tiết kiệm gas và cho phép typed error parsing.

{% hint style="info" %}
**Khi contract revert**, bytes 4 đầu tiên là error selector. Dùng viem `decodeErrorResult` hoặc ABI để decode thành error name. Xem [Error Handling](../developers/38-common-errors.md) cho code pattern.
{% endhint %}

## Diamond / MarketFacet

| Error | Ý nghĩa | Fix |
|---|---|---|
| `Market_NotFound()` | `marketId` không tồn tại | Kiểm tra lại marketId |
| `Market_EmptyQuestion()` | `createMarket` với question rỗng | Truyền non-empty string |
| `Market_InvalidEndTime()` | `endTime <= block.timestamp` | Chỉ định `endTime` trong tương lai |
| `Market_Ended()` | Market đã qua `endTime` | Không thể trade sau endTime |
| `Market_Resolved()` | Market đã resolve | Không gọi `splitPosition` / `mergePositions` sau resolve |
| `Market_NotResolved()` | Chưa resolve mà gọi `redeem` | Đợi oracle resolve + gọi `resolveMarket` |
| `Market_OracleNotApproved()` | Oracle address chưa được admin approve (tại create hoặc resolve) | Admin gọi `setApprovedOracle` |
| `Market_OracleNotResolved()` | Oracle chưa có kết quả | Đợi oracle report |
| `Market_RedemptionFeeTooHigh()` | Fee bps > `MAX_REDEMPTION_FEE_BPS (1500)` | Giảm fee xuống ≤ 15% |
| `Market_ExceedsPerMarketCap()` | Vượt TVL cap của market | Chia nhỏ giao dịch hoặc chờ cap raise |
| `Market_EmergencyDelayNotMet()` | Gọi `emergencyResolve` trước `endTime + 7 days` | Chờ đủ 7 ngày |
| `Market_GracePeriodNotEnded()` | Gọi `sweepUnclaimed` trước `resolvedAt + 365 days` | Chờ đủ 1 năm |
| `Market_RefundModeActive()` | Market đã refund mode, không split/merge được | Dùng `refund` thay thế |
| `Market_RefundModeNotActive()` | Gọi `refund` khi market không ở refund mode | Không áp dụng |
| `Market_InsufficientBalance()` | Không đủ token để redeem/refund | Kiểm tra balance |

## Pausable

| Error | Ý nghĩa | Fix |
|---|---|---|
| `Pausable_Paused(bytes32 module)` | Module đang paused | Đợi admin unpause hoặc dùng function không bị ảnh hưởng |

## AccessControl

| Error | Ý nghĩa | Fix |
|---|---|---|
| `AC_Unauthorized(bytes32 role, address caller)` | Caller thiếu role yêu cầu | Request role hoặc dùng address đã được grant |
| `AC_LastAdminProtection()` | Revoke last `DEFAULT_ADMIN_ROLE` bị block | Grant admin cho address mới trước khi revoke |

## DiamondCut

| Error | Ý nghĩa | Fix |
|---|---|---|
| `DC_SelectorAlreadyExists(bytes4)` | `Add` selector đã tồn tại | Dùng `Replace` thay vì `Add` |
| `DC_SelectorNotFound(bytes4)` | `Replace`/`Remove` selector không tồn tại | Kiểm tra selector hex |
| `DC_ProtectedSelector(bytes4)` | Không thể remove selector được bảo vệ (`diamondCut`, loupe core) | Không áp dụng |

## Exchange

| Error | Ý nghĩa | Fix |
|---|---|---|
| `Exchange_InvalidPrice(uint256)` | Price ngoài range `[10000, 990000]` hoặc không step `10000` | Đặt price trong `$0.01`–`$0.99`, step `$0.01` |
| `Exchange_OrderTooSmall(uint256)` | Amount < `MIN_ORDER_SIZE (1000000 = $1)` | Tăng amount ≥ $1 |
| `Exchange_MaxOrdersExceeded()` | User đã có 50 orders pending | Cancel orders cũ trước khi place thêm |
| `Exchange_OrderNotFound(bytes32)` | `cancelOrder` / `getOrder` với orderId không tồn tại | Kiểm tra orderId |
| `Exchange_NotOrderOwner(address)` | Cancel lệnh của người khác khi market chưa kết thúc | Chỉ cancel lệnh của chính mình (sau end time anyone can cancel) |
| `Exchange_MarketNotActive()` | Market paused/resolved/refund mode | Kiểm tra `getMarket` |
| `Exchange_InsufficientDeposit()` | Not enough USDC/token để post order | Approve + transfer đủ |

## Router

| Error | Ý nghĩa | Fix |
|---|---|---|
| `Router_SlippageExceeded(uint256 expected, uint256 actual)` | `amountOut < minOut` | Tăng slippage tolerance hoặc retry với quote mới |
| `Router_DeadlineExpired(uint256 deadline, uint256 now)` | TX executed sau `deadline` | Dùng deadline xa hơn (thường 5 phút) |
| `Router_NonZeroBalance()` | Post-call balance ≠ 0 (INV-3 violation) | Đây là critical — report security |
| `Router_InvalidMarket()` | marketId không tồn tại hoặc chưa register Hook pool | Kiểm tra deployed addresses + pool registration |

## Hook

| Error | Ý nghĩa | Fix |
|---|---|---|
| `Hook_NotPoolManager()` | `unlockCallback` / hook callback bị gọi từ address ngoài `POOL_MANAGER` | Không call trực tiếp; dùng Router |
| `Hook_IdentityCommitMissing()` | Swap vào pool mà không có identity commit (anti-sandwich) | Swap qua Router whitelist (setTrustedRouter) |
| `Hook_UntrustedRouter(address)` | Router gọi commit nhưng không nằm trong whitelist | Admin add router qua `setTrustedRouter(..., true)` |
| `Hook_PoolNotRegistered(PoolId)` | Swap pool chưa register marketId mapping | Diamond phải gọi `registerMarketPool` trước |
| `Hook_UpgradeTimelockNotMet(uint256 readyAt)` | `executeUpgrade` trước `proposedAt + 48h` | Chờ đủ 48h |
| `Hook_UpgradeNoPending()` | `executeUpgrade`/`cancelUpgrade` khi không có pending | Gọi `proposeUpgrade` trước |

## Oracle

### ManualOracle

| Error | Ý nghĩa | Fix |
|---|---|---|
| `Oracle_AlreadyResolved()` | Gọi `resolve` lần 2 (INV-6) | Oracle result là immutable |
| `Oracle_NotAdmin()` | Non-admin gọi `resolve` | Dùng address có role |

### ChainlinkOracle

| Error | Ý nghĩa | Fix |
|---|---|---|
| `Oracle_StalePrice()` | `updatedAt` > 24h cũ | Chờ feed update; check Chainlink status |
| `Oracle_InvalidPrice()` | Price ≤ 0 | Kiểm tra feed — có thể feed bị depegged |
| `Oracle_SequencerDown()` | L2 sequencer downtime | Chờ sequencer khôi phục |
| `Oracle_RoundIncomplete()` | `answeredInRound < roundId` | Chờ round complete |
| `Oracle_ResolveTimeNotReached()` | `block.timestamp < resolveTime` | Chờ đủ thời gian |
| `Oracle_MarketNotBound()` | Market chưa được `bindMarket` | Admin bind trước |

## ReentrancyGuard

| Error | Ý nghĩa | Fix |
|---|---|---|
| Raw revert (no selector) | Reentrancy detected — `tload(slot) != 0` | Không gọi lại function đã trong flight |

## Debug tip: decode error selector

```typescript
import { decodeErrorResult } from 'viem';
import MarketFacetAbi from './abis/MarketFacet.json';

try {
  await walletClient.writeContract({ ... });
} catch (err) {
  const revertData = err.data;  // 0x + 8 hex chars = selector, rest = args
  const decoded = decodeErrorResult({
    abi: MarketFacetAbi,
    data: revertData,
  });
  console.log(decoded.errorName, decoded.args);
  // e.g., "Market_OracleNotApproved", []
}
```

Xem full pattern: [Error Handling](../developers/38-common-errors.md).
