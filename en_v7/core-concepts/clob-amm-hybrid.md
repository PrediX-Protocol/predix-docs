---
description: Hybrid Liquidity mechanisms from PrediX
---

# CLOB + AMM hybrid

PrediX combines 2 liquidity mechanisms: an on-chain order book (CLOB) + Uniswap v4 pool (AMM). The Router automatically selects the best path within the same transaction.

### Why Hybrid

| Model           | <mark style="color:$primary;">**CLOB (Polymarket)**</mark> | <mark style="color:pink;">**AMM (Uniswap)**</mark> | <mark style="color:$warning;">**Hybrid (PrediX)**</mark> |
| --------------- | ---------------------------------------------------------- | -------------------------------------------------- | -------------------------------------------------------- |
| Small trades    | OK but wide slippage if few makers                         | Smooth, low slippage                               | Smooth + price improvement when makers are present       |
| Large trades    | Depends on maker depth                                     | Slippage increases with size                       | Drain CLOB first, AMM for the remainder                  |
| Maker incentive | Limit order (no fee)                                       | Only LPs earn fees                                 | **Both** — makers place orders, LPs provide liquidity    |
| Fair pricing    | Makers set their own                                       | AMM curve                                          | AMM = floor, CLOB = price improvement                    |
| MEV protection  | Order book harder to frontrun                              | Pool vulnerable to sandwich                        | Hook anti-sandwich + identity commit                     |

***

### Router — Single Entry Point

The Router is **stateless** — the invariant `balanceOf(router) == 0` is enforced on-chain after every public call. No custody, no stuck funds.

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

<sub>Comparison of CLOB vs AMM vs Hybrid PrediX</sub>

#### <mark style="color:orange;">How the Execution Router Works</mark>

When you sell, the PrediX Router automatically finds the most efficient path to give you the best price by following this hierarchy:

1. CLOB Liquidity: It first "drains" existing bid orders on the Central Limit Order Book (matching you with users waiting to buy YES).
2. AMM Swap: If the order book depth is insufficient, it swaps the remaining tokens through the Automated Market Maker (Liquidity Pool).
3. Synthetic Routing: In specific scenarios, the router uses a synthetic route to maximize your return:
   * It buys NO tokens using fresh USDC.
   * It merges those NO tokens with your YES tokens (which equals 1 unit of the underlying collateral).
   * It pays out the resulting USDC to you.

***

### CLOB — On-Chain Order Book

Contract: `PrediXExchange`.

* **Tick size**: 99 price levels at $0.01, $0.02, ..., $0.99. Stored on compressed bitmaps.
* **Limit order**: User selects side (BUY\_YES / SELL\_YES / BUY\_NO / SELL\_NO), price, and amount. Token or USDC deposit is locked until filled or cancelled.
* **Makers** place limit orders and wait for fills. **Takers** execute against them as market orders.

#### <mark style="color:$warning;">3 CLOB Match Types</mark>

All 3 satisfy: **no one is disadvantaged** — each side accepts their own price.

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

<sub>3 CLOB match types PrediX</sub>



* **Complementary**: BUY\_YES ↔ SELL\_YES in the same market. Most common.
* **Mint** (synthetic): BUY\_YES + BUY\_NO ≥ $1. Diamond mints a pair, delivers YES to the YES buyer and NO to the NO buyer. Spread → protocol.
* **Merge** (synthetic): SELL\_YES + SELL\_NO ≤ $1. Diamond burns a pair, returns USDC to both sellers. Spread → protocol.

***

### AMM — Uniswap v4 Pool

Each market has 1-2 v4 pools: YES-USDC and optionally NO-USDC. **PrediX Hook** plugs into v4:

| Callback                | Function                                                                                                      |
| ----------------------- | ------------------------------------------------------------------------------------------------------------- |
| `beforeSwap`            | Verify anti-sandwich identity (Router must commit identity first, Hook checks via transient storage EIP-1153) |
| `beforeAddLiquidity`    | Block adding LP if market is resolved / refunded                                                              |
| `beforeRemoveLiquidity` | Track pool registration                                                                                       |
| `beforeDonate`          | Block donations after endTime (prevent brute-force payout attacks)                                            |

The Hook **does not hold user funds long-term**. LPs receive LP tokens per the v4 PositionManager standard.

{% hint style="info" %}
### Trading Directly on AMM

* The YES-USDC pool is a standard v4 pool — you can swap via UniversalRouter, Uniswap web, or PoolManager directly.
* **However**: bypassing CLOB liquidity → price may be worse. Always use `PrediXRouter` to take advantage of both.
{% endhint %}

***

### When Does the Router Prefer CLOB Over AMM

The Router **always** checks CLOB first:

1. If CLOB has orders at a better price than AMM spot → fills CLOB.
2. Partially fills CLOB, routes the remainder to AMM if CLOB depth is insufficient.
3. If CLOB reverts (insufficient token match, price deviation) → Router skips, emits `ClobSkipped(reason)` event, falls back entirely to AMM.

Users don't need to worry — the Router always returns the best price within the same tx.

***

### MEV Protection

PrediX Hook implements **identity commit** to prevent sandwich attacks:

<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

<sub>Anti-sandwich MEV PrediX</sub>

MEV bots cannot frontrun + backrun your trade within the same block — the Hook reverts if identity doesn't match.
