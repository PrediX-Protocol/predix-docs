---
description: Mapping đầy đủ SC event → handler file → table updated.
---

# Events Mapping (SC → Table)

Bảng tra cứu events được index + ảnh hưởng tables nào.

## MarketFacet (Diamond)

| Event | Handler | Tables |
|---|---|---|
| `MarketCreated` | `MarketFacet.ts` | `market` (insert); `protocolStats.totalMarkets++`; factory-subscribe OutcomeToken |
| `PositionSplit` | `MarketFacet.ts` | `positionSplit` (audit insert); `position.yesBalance += amount`; `market.totalCollateral += amount`; `protocolStats.totalVolume += 0` (split = collateral, không volume) |
| `PositionMerged` | `MarketFacet.ts` | `positionMerge`; `position.yesBalance -= amount`; `market.totalCollateral -= amount` |
| `TokensRedeemed` | `MarketFacet.ts` | `redemption` insert; `position.totalReceived += payout`; `market.totalCollateral -= amount` |
| `MarketResolved` | `MarketFacet.ts` | `market.isResolved=true`, `outcome`, `resolvedAt`, `resolutionType='ORACLE'`; bulk update `position.outcomeAtResolve` |
| `MarketEmergencyResolved` | `MarketFacet.ts` | `market.resolutionType='EMERGENCY'`; same as ORACLE |
| `RefundModeEnabled` | `MarketFacet.ts` | `market.refundModeActive=true`, `refundEnabledAt`, `resolutionType='REFUND'`; bulk update `position.outcomeAtResolve='refund'` |
| `MarketRefunded` | `MarketFacet.ts` | `refundClaim` insert; `position.totalReceived += amount` |
| `UnclaimedSwept` | `MarketFacet.ts` | `market.sweptAt = now` |
| `OracleApproved` / `OracleRevoked` | `MarketFacet.ts` | `oracleApprovalAudit` insert |
| `FeeRecipientUpdated` | `MarketFacet.ts` | `protocolStats` — track admin change |
| `MarketCreationFeeUpdated` | `MarketFacet.ts` | audit trail |
| `DefaultPerMarketCapUpdated` | `MarketFacet.ts` | audit trail |
| `PerMarketCapUpdated` | `MarketFacet.ts` | `market.perMarketCap = new` |
| `DefaultRedemptionFeeUpdated` | `MarketFacet.ts` | audit only — existing markets không affect (snapshot FINAL-H04) |
| `PerMarketRedemptionFeeUpdated` | `MarketFacet.ts` | `market.perMarketRedemptionFeeBps`, `redemptionFeeOverridden=true` |

## EventFacet (Diamond)

| Event | Handler | Tables |
|---|---|---|
| `EventCreated` | `EventFacet.ts` | `event_group` insert; per child market set `market.eventId` |
| `EventResolved` | `EventFacet.ts` | `event_group.isResolved`, `winningIndex`, `winningChildMarketId`; cascade update child markets (1 winner YES, rest NO) |
| `EventRefundModeEnabled` | `EventFacet.ts` | `event_group.refundModeActive=true`; cascade children |

## AccessControlFacet (Diamond)

| Event | Handler | Tables |
|---|---|---|
| `RoleGranted` | `AccessControlFacet.ts` | `roleGrantAudit` insert |
| `RoleRevoked` | `AccessControlFacet.ts` | `roleGrantAudit` insert |
| `RoleAdminChanged` | `AccessControlFacet.ts` | `roleGrantAudit` insert |

## PausableFacet (Diamond)

| Event | Handler | Tables |
|---|---|---|
| `ModulePaused` | `PausableFacet.ts` | `pauseStateChange` insert |
| `ModuleUnpaused` | `PausableFacet.ts` | `pauseStateChange` insert |

## DiamondCutFacet (Diamond)

| Event | Handler | Tables |
|---|---|---|
| `DiamondCut` | `DiamondCutFacet.ts` | `diamondCutHistory` insert (facets[], init, calldata) |

## PrediXRouter (canonical volume source)

| Event | Handler | Tables |
|---|---|---|
| `Trade` | `PrediXRouter.ts` | `trade` insert; `market.volume += amountIn`; `market.tradeCount++`; `market.lastTradeAt = now`; `position.totalSpent/Received`; `userStats.totalVolume`, `tradeCount`; `protocolStats.totalVolume`, `totalTrades`; `priceSnapshot(source=router)` |
| `DustRefunded` | `PrediXRouter.ts` | **no-op** — đã accounted trong `Trade` |

## PrediXExchange (CLOB)

| Event | Handler | Tables |
|---|---|---|
| `OrderPlaced` | `PrediXExchange.ts` | `exchangeOrder` insert với status `OPEN` |
| `OrderMatched` | `PrediXExchange.ts` | Update maker + taker `exchangeOrder.filled`; insert `takerFill` nếu taker không phải Router |
| `OrderCancelled` | `PrediXExchange.ts` | `exchangeOrder.status='CANCELLED'`, `cancelledAt` |
| `FeeCollected` | `PrediXExchange.ts` | `protocolStats.totalFeesCollected += amount` |

## PrediXHookV2

| Event | Handler | Tables |
|---|---|---|
| `Hook_PoolRegistered` | `PrediXHookV2.ts` | `hookPoolBinding` insert |
| `Hook_MarketTraded` | `PrediXHookV2.ts` | `priceSnapshot(source=hook_amm)` — analytics-only (không count volume — Router là canonical) |
| `Hook_AdminUpdated` | `PrediXHookV2.ts` | audit |
| `Hook_AdminChangeProposed` | `PrediXHookV2.ts` | audit |
| `Hook_PauseStatusChanged` | `PrediXHookV2.ts` | audit |
| `Hook_TrustedRouterUpdated` | `PrediXHookV2.ts` | audit |

## PrediXHookProxyV2 (upgrade lifecycle)

| Event | Handler | Tables |
|---|---|---|
| `UpgradeProposed` | `PrediXHookProxyV2.ts` | `hookUpgradeHistory` insert (`status=proposed`) |
| `Upgraded` | `PrediXHookProxyV2.ts` | `hookUpgradeHistory` update (`status=executed`) |
| `UpgradeCancelled` | `PrediXHookProxyV2.ts` | `hookUpgradeHistory` update (`status=cancelled`) |
| `AdminChangeProposed` | `PrediXHookProxyV2.ts` | audit |
| `AdminChanged` | `PrediXHookProxyV2.ts` | audit |
| `TimelockDurationUpdated` | `PrediXHookProxyV2.ts` | audit |
| `InitReverted` | `PrediXHookProxyV2.ts` | audit |

## ManualOracle

| Event | Handler | Tables |
|---|---|---|
| `ResolutionSet` | `ManualOracle.ts` | audit — market sẽ resolve qua `resolveMarket` sau |
| `AdminChanged` | `ManualOracle.ts` | audit |

## ChainlinkOracle

| Event | Handler | Tables |
|---|---|---|
| `MarketBound` | `ChainlinkOracle.ts` | audit |
| `Resolved` | `ChainlinkOracle.ts` | audit |

## Uniswap PoolManager (pricing layer — DRAFT)

| Event | Handler | Tables |
|---|---|---|
| `Initialize` | `PoolManager.ts` | `ammPoolState` insert |
| `Swap` | `PoolManager.ts` | `ammPoolState` update (sqrtPrice, tick) |
| `ModifyLiquidity` | `PoolManager.ts` | `ammPoolState.liquidity` update |

## OutcomeToken (factory-subscribed)

Ponder auto-subscribe `Transfer` event mỗi khi `MarketCreated` emit, từ block đó forward.

| Event | Handler | Tables |
|---|---|---|
| `Transfer` | `OutcomeToken.ts` | (Optional) position balance sync — currently relies on Router.Trade + split/merge events. |

## Protocol contract filter

Source: `INDEXER/src/lib/protocolContracts.ts`

Handler dùng `isProtocolContract(address)` để skip update `position` / `userStats` cho addresses:

- Diamond
- Router
- Exchange
- Hook Proxy
- PoolManager

Lý do: các contract này "trade" on behalf users — nếu count vào `position.user = Router` → user phantom xuất hiện. Chỉ real end-users mới có position row.

Audit trails (`positionSplit`, `positionMerge`, `diamondCutHistory`…) vẫn write cho mọi sender, kể cả protocol contract — đảm bảo audit complete.
