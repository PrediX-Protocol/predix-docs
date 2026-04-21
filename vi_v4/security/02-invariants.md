---
description: 7 invariants critical của PrediX protocol. Tested qua Foundry invariant fuzz.
---

# Critical Invariants (7)

Đây là **7 bất biến cứng** của protocol. Violation của bất kỳ invariant nào = critical bug cần disclose ngay.

Tất cả invariants được tested qua Foundry `invariant_*` functions, fuzz với 10K+ runs mỗi CI run.

## INV-1: Collateral Solvency

**Statement**: Với mỗi market chưa resolve:

```
YES.totalSupply == NO.totalSupply == market.totalCollateral
```

**Why**: Mỗi token YES hoặc NO được backed bởi 1 USDC collateral (1:1). Violation → protocol insolvent hoặc print money.

**Where enforced**:
- `Diamond.splitPosition`: mint `amount` YES + `amount` NO, `totalCollateral += amount`
- `Diamond.mergePositions`: burn pair, `totalCollateral -= amount`
- `Diamond.refund` (refund mode): pro-rata burn + return

**Test**: `diamond/test/invariants/CollateralSolvency.t.sol`

---

## INV-2: Exchange Solvency

**Statement**: Với mỗi thời điểm:

```
Σ order.depositLocked == USDC.balanceOf(exchange) + Σ outcomeToken.balanceOf(exchange)
```

**Why**: Mọi USDC/YES/NO trong PrediXExchange contract đều phải có reference từ một pending order. Violation → dust leak hoặc stuck funds.

**Where enforced**:
- `placeOrder`: lock deposit khi order post
- `cancelOrder` / `OrderMatched`: unlock khi order complete
- `fillAsChain`: transfer in/out symmetric

**Test**: `exchange/test/invariants/ExchangeSolvency.t.sol`

---

## INV-3: Router Non-Custody

**Statement**: Sau mỗi public function call vào `PrediXRouter`:

```
USDC.balanceOf(router) == 0
YES.balanceOf(router) == 0 (per market)
NO.balanceOf(router) == 0 (per market)
```

**Why**: Router là stateless aggregator — không phép giữ fund. Violation → attacker có thể drain dust tích luỹ.

**Where enforced**: Mọi public entry point kết thúc bằng `_refundAndAssertZero()` — transfer dust về caller + assert balance = 0.

**Test**: `router/test/invariants/RouterZeroCustody.t.sol`

---

## INV-4: Redemption Fee Bound

**Statement**: Với mỗi market:

```
effectiveRedemptionFeeBps <= MAX_REDEMPTION_FEE_BPS (1500 = 15%)
```

**Why**: Fee vượt 15% ⇒ user bị ăn > 15% profit → abuse risk. Hard cap ngăn cả admin không thể raise.

**Where enforced**:
- `setDefaultRedemptionFeeBps`: revert `Market_RedemptionFeeTooHigh` nếu > 1500
- `setPerMarketRedemptionFeeBps`: same check
- `snapshottedRedemptionFeeBps` tại `createMarket`: snapshot <= 1500

**Test**: `diamond/test/invariants/RedemptionFeeBound.t.sol`

---

## INV-5: Hook Identity Commit

**Statement**: Mọi `beforeSwap` call on a PrediX Hook pool require identity commit:

```
if (transient_storage[commitSlot] == 0) revert Hook_IdentityCommitMissing
```

**Why**: Anti-sandwich mechanism. Sandwich attacker không trong Router whitelist → không commit được → swap reject.

**Where enforced**:
- Hook `beforeSwap`: check `tload(commitSlot)` non-zero
- Router `buyYes/sellYes/...`: commit trước `poolManager.unlock`

**Test**: `hook/test/invariants/IdentityCommit.t.sol`, `router/test/fork/PrediXRouter_HookCommit.fork.t.sol`

---

## INV-6: Resolution Monotonicity

**Statement**: Một khi `market.isResolved == true`:

```
MarketData.isResolved = true, outcome = X, resolvedAt = Y
```

Là **immutable** — không function nào có thể revert about.

**Why**: Nếu resolution có thể revert, user redeem rồi lại bị "unresolve" → lỗi double-spend hoặc loss của integrity.

**Where enforced**:
- Không facet function nào touch `isResolved` sau khi set
- `refundModeActive` là flag độc lập, chỉ active khi `isResolved == false` tại time gọi `enableRefundMode`

**Test**: `diamond/test/invariants/ResolutionMonotonic.t.sol`

---

## INV-7: Outcome Token Supply

**Statement**: Chỉ Diamond contract được mint/burn OutcomeToken:

```
for all tx, for all OutcomeToken T:
  T.mint(...) callable only by T.factory() == Diamond
  T.burn(...) callable only by T.factory() == Diamond
```

**Why**: Nếu non-Diamond mint → tăng `totalSupply` mà không `totalCollateral` tương ứng → break INV-1.

**Where enforced**:
- `OutcomeToken.mint` / `burn`: modifier `onlyFactory` — `msg.sender == factory`
- Factory bound immutable tại deploy (constructor)

**Test**: `shared/test/invariants/OutcomeTokenSupply.t.sol`

---

## Invariant test summary

| Invariant | Test file | Fuzz runs / CI |
|---|---|---|
| INV-1 Collateral Solvency | `CollateralSolvency.t.sol` | 10K |
| INV-2 Exchange Solvency | `ExchangeSolvency.t.sol` | 10K |
| INV-3 Router Non-Custody | `RouterZeroCustody.t.sol` | 10K |
| INV-4 Redemption Fee Bound | `RedemptionFeeBound.t.sol` | 10K |
| INV-5 Hook Identity Commit | `IdentityCommit.t.sol` | 10K |
| INV-6 Resolution Monotonic | `ResolutionMonotonic.t.sol` | 10K |
| INV-7 OutcomeToken Supply | `OutcomeTokenSupply.t.sol` | 10K |

## Report violation

Nếu bạn phát hiện một tx/state trên chain violate bất kỳ invariant nào → **đây là critical**. Report ngay theo [Bug Bounty](03-bug-bounty.md) với payload evidence:

- Block number
- Tx hash
- Selector trigger
- State snapshot before/after
- Reasoning tại sao violate

Critical invariant violation có payout max theo bounty tier.
