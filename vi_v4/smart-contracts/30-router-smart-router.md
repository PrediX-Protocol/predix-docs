---
description: PrediXRouter — stateless aggregator giữa CLOB + AMM, Permit2-native, zero-custody invariant.
---

# PrediXRouter (Smart Router)

`PrediXRouter` tổng hợp thanh khoản từ 2 nguồn (Exchange CLOB + Hook AMM qua Uniswap v4) để cung cấp best execution cho trader. Router **không giữ fund** — mọi call đều assert `balanceOf(router) == 0` post-execution.

Source: `SC/packages/router/src/PrediXRouter.sol`
Interface: `SC/packages/router/src/interfaces/IPrediXRouter.sol`

## Nguyên tắc thiết kế

| Nguyên tắc | Hiệu ứng |
|---|---|
| **Stateless** | Zero storage. Tất cả data đi qua calldata/memory. Upgrade = redeploy thuần. |
| **Zero-custody** | Sau mỗi public function, contract self-assert `_refundAndAssertZero()` — balance = 0. |
| **Permit2-native** | User `permit2.permitTransferFrom` → Router pull trực tiếp. Không cần 2 tx. |
| **Quote & revert** | `quote*` functions simulate rồi revert với encoded data — dùng v4-quoter pattern. |
| **Trusted by Hook** | Router whitelist qua `Hook.setTrustedRouter`. Router commit identity trong transient storage. |

## Enum TradeType (quan trọng)

```solidity
enum TradeType {
    BUY_YES,   // 0
    SELL_YES,  // 1
    BUY_NO,    // 2
    SELL_NO    // 3
}
```

Event `Trade` emit `uint8 tradeType` khớp enum trên — canonical cho indexer volume/trade feed.

## Trading functions

### `buyYes` / `sellYes`

```solidity
function buyYes(
    uint256 marketId,
    uint256 usdcIn,
    uint256 minYesOut,
    address recipient,
    uint256 deadline
) external returns (uint256 yesOut);

function sellYes(
    uint256 marketId,
    uint256 yesIn,
    uint256 minUsdcOut,
    address recipient,
    uint256 deadline
) external returns (uint256 usdcOut);
```

**Flow buyYes**:
1. Pull `usdcIn` USDC từ caller (ERC20 allowance hoặc Permit2)
2. Try fill trên CLOB nếu có ask tốt hơn AMM → `Exchange.fillAsChain(..., BUY_YES)`
3. Route phần còn lại qua v4 pool USDC/YES → AMM swap
4. Check `yesOut >= minYesOut` → else `Router_SlippageExceeded`
5. Transfer YES → recipient
6. `_refundAndAssertZero()` — trả lại USDC dư (nếu có) + assert balance = 0

### `buyNo` / `sellNo` (virtual NO pricing)

```solidity
function buyNo(
    uint256 marketId,
    uint256 usdcIn,
    uint256 mintAmount,   // hint cho Diamond mint amount
    uint256 minNoOut,
    address recipient,
    uint256 deadline
) external returns (uint256 noOut);

function sellNo(...) external returns (uint256 usdcOut);
```

**Flow buyNo**: Vì chỉ có pool YES/USDC, Router mô phỏng NO:
1. Split USDC → mint pair (YES + NO) qua Diamond
2. Swap YES → USDC trên AMM
3. Giữ NO, trả recipient

Chi tiết: [Virtual NO Pricing](../giao-dich/26-virtual-no-pricing.md).

## Quote functions (view + revert-and-decode)

```solidity
function quoteBuyYes(uint256 marketId, uint256 usdcIn) external returns (uint256 yesOut);
function quoteSellYes(uint256 marketId, uint256 yesIn) external returns (uint256 usdcOut);
function quoteBuyNo(uint256 marketId, uint256 usdcIn) external returns (uint256 noOut);
function quoteSellNo(uint256 marketId, uint256 noIn) external returns (uint256 usdcOut);
```

Dùng v4-quoter pattern: function simulate full swap rồi `revert(abi.encode(quoteResult))`. Caller decode result từ revert data.

```typescript
// viem example
try {
  await publicClient.simulateContract({
    address: ROUTER, abi, functionName: "quoteBuyYes",
    args: [marketId, usdcIn],
  });
} catch (err) {
  const decoded = decodeQuoteError(err);  // decode từ revert data
  console.log("Expected YES out:", decoded.yesOut);
}
```

Xem [Trading Integration](../developers/35-trading-integration.md) cho full example.

## Flash Accounting (v4 unlock callback)

```solidity
function unlockCallback(bytes calldata data) external returns (bytes memory);
```

**Role**: Chỉ `PoolManager` gọi được — `msg.sender == POOL_MANAGER`.
Dùng Uniswap v4 unlock pattern: Router mở "flash session" với PoolManager, thực hiện chuỗi swap + mint + burn, kết thúc với delta = 0.

## Zero-custody invariant

Mọi public entry point kết thúc bằng `_refundAndAssertZero()`:

```solidity
function _refundAndAssertZero() internal {
    uint256 usdcBalance = USDC.balanceOf(address(this));
    if (usdcBalance > 0) USDC.safeTransfer(msg.sender, usdcBalance);
    // Similarly for YES/NO per market
    require(USDC.balanceOf(address(this)) == 0, "Router_NonZeroBalance");
}
```

Đây là INV-3 trong [Critical Invariants](../security/02-invariants.md) — test suite assert invariant này qua 83 test cases.

## Identity Commit (anti-sandwich)

Trước khi gọi v4 swap, Router commit caller identity vào transient storage của Hook:

```solidity
function buyYes(...) external {
    // ... pull tokens, start unlock
    hook.commitIdentity(msg.sender);  // EIP-1153 transient write
    poolManager.unlock(abi.encode(...));  // triggers beforeSwap → hook verify
    // transient storage auto-reset at tx end
}
```

Hook trong `beforeSwap` verify `msg.sender == POOL_MANAGER` và committed identity khớp expected trader. Nếu không có commit → Hook revert `Hook_IdentityCommitMissing`. Chi tiết: [PrediXHookV2](31-hook-uniswap-v4.md).

## Events

```solidity
event Trade(
    uint256 indexed marketId,
    address indexed trader,
    address indexed recipient,
    uint8 tradeType,       // TradeType enum
    uint256 amountIn,
    uint256 amountOut,
    uint256 clobFilled,    // amount filled via CLOB
    uint256 ammFilled,     // amount filled via AMM
    uint256 yesPrice       // post-trade YES price snapshot
);

event DustRefunded(address indexed recipient, address token, uint256 amount);
```

{% hint style="info" %}
**Router.Trade là canonical source cho volume/trade feed**. Indexer count volume từ event này, không từ `Hook_MarketTraded` (để tránh double-count với CLOB path). Xem [Indexer Overview](../indexer/00-overview.md).
{% endhint %}

## Best practices cho integrator

1. **Luôn đặt `deadline`** — thường 5 phút. Tránh lệnh cũ bị execute muộn.
2. **Luôn đặt `minOut`** ≥ 98% quote (2% slippage). Quote có thể stale do block gap.
3. **Dùng Permit2** thay vì 2-tx approve → tiết kiệm gas + UX tốt hơn.
4. **Check event `Trade`** sau mỗi tx để confirm fill amount.
5. **Trong bot**: subscribe WS `Trade` event trên Router để react realtime.

Xem [Trading Integration](../developers/35-trading-integration.md) cho complete code examples.
