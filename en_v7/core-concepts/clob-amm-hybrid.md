---
description: Hybrid Liquidity mechanisms from PrediX
---

# CLOB + AMM hybrid

PrediX combines 2 liquidity mechanisms: an on-chain order book (CLOB) + a Uniswap v4 pool (AMM). For every trade the Router prices **both** venues and routes for best execution within the same transaction.

### Why Hybrid

| Model           | <mark style="color:$primary;">**CLOB (Polymarket)**</mark> | <mark style="color:pink;">**AMM (Uniswap)**</mark> | <mark style="color:$warning;">**Hybrid (PrediX)**</mark>      |
| --------------- | ---------------------------------------------------------- | -------------------------------------------------- | ------------------------------------------------------------- |
| Small trades    | OK but wide slippage if few makers                         | Smooth, low slippage                               | Smooth + price improvement when makers are present            |
| Large trades    | Depends on maker depth                                     | Slippage increases with size                       | Price both venues, fill CLOB only where it beats the AMM, route the remainder to AMM |
| Maker incentive | Limit order (no fee)                                       | Only LPs earn fees                                 | **Both** - makers place orders, LPs provide liquidity         |
| Fair pricing    | Makers set their own                                       | AMM curve                                          | AMM-effective = floor, CLOB = price improvement on top        |
| MEV protection  | Order book harder to frontrun                              | Pool vulnerable to sandwich                        | Hook anti-sandwich + identity commit                          |

***

### Router - Single Entry Point

The Router is **stateless** - the invariant `balanceOf(router) == 0` is enforced on-chain after every public call (USDC, YES and NO are each refunded to the caller and asserted to zero). No custody, no stuck funds.

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

<sub>Comparison of CLOB vs AMM vs Hybrid PrediX</sub>

#### <mark style="color:orange;">How the Execution Router Works</mark>

For every trade the Router prices **both** venues and routes for best execution — it does **not** blindly drain the order book first:

1. **Quote the AMM.** The Router computes the AMM **effective** price (size-adjusted and fee-included) for your trade size — not the spot price.
2. **Let the CLOB compete.** The CLOB leg is **capped at that AMM-effective price**, so the order book fills **only** the portion priced as good as or better than the AMM. The Router converges on the optimal split: the size where the marginal CLOB order equals the AMM-effective price for the leftover.
3. **Execute.** One CLOB fill (bounded by the cap) + one AMM swap for the remainder. If the AMM is cheaper across your whole size, the CLOB fills ~nothing and the AMM takes everything; if the CLOB is better, it fills more. Either way you get the best blended price.

***

### CLOB - On-Chain Order Book

Contract: `PrediXExchange`.

* **Tick size**: 99 price levels at $0.01, $0.02, ..., $0.99. Stored on compressed bitmaps.
* **Limit order**: User selects side (BUY\_YES / SELL\_YES / BUY\_NO / SELL\_NO), price, and amount. Token or USDC deposit is locked until filled or cancelled.
* **Makers** place limit orders and wait for fills. **Takers** execute against them as market orders.

#### <mark style="color:$warning;">3 CLOB Match Types</mark>

All 3 satisfy: **no one is disadvantaged** - each side accepts their own price.

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

<sub>3 CLOB match types PrediX</sub>



* **Complementary**: BUY\_YES ↔ SELL\_YES in the same market. Most common.
* **Mint** (synthetic): BUY\_YES + BUY\_NO ≥ $1. The Diamond mints a pair, delivering YES to the YES buyer and NO to the NO buyer. Any surplus (their combined price above $1) is refunded to the taker as price improvement — **the protocol charges no per-trade fee**.
* **Merge** (synthetic): SELL\_YES + SELL\_NO ≤ $1. The Diamond burns a pair, returning USDC to both sellers. Surplus is **zero by construction** — **no per-trade fee**.

***

### AMM - Uniswap v4 Pool

**Each market has exactly one v4 pool: YES-USDC.** There is no NO-USDC pool — NO is priced as `1 − YES` and traded synthetically through the YES pool: buying NO mints a YES/NO pair and sells the YES leg into the pool; selling NO buys YES from the pool and merges it with your NO back into collateral. (Anyone could permissionlessly deploy a separate NO-USDC pool on Uniswap, but it cannot register with the PrediX Hook and the Router never routes to it.)

**PrediX Hook** plugs into v4:

| Callback                | Function                                                                                                                  |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `beforeSwap`            | Verify anti-sandwich identity (the Router must commit identity first; the Hook checks it via transient storage, EIP-1153) |
| `beforeAddLiquidity`    | Block adding LP when the market is resolved / in refund mode / past endTime, and bound liquidity to the \[0,1] YES-price band |
| `beforeRemoveLiquidity` | Verify the pool is registered (LPs can always exit)                                                                      |
| `beforeDonate`          | Block donations after endTime (prevent brute-force payout attacks)                                                       |

The Hook **does not hold user funds long-term**. LPs hold standard Uniswap v4 liquidity positions (via the PositionManager).

{% hint style="info" %}
### Trading Directly on AMM

* The YES-USDC pool is a standard v4 pool - you can swap via UniversalRouter, Uniswap web, or PoolManager directly.
* **However**: bypassing CLOB liquidity → price may be worse. Always use `PrediXRouter` to take advantage of both.
{% endhint %}

***

### When Does the Router Prefer CLOB Over AMM

The Router prices the AMM **first**, then lets the CLOB compete against that price:

1. It quotes the AMM **effective** price (size-adjusted) — not spot.
2. The CLOB leg is capped at that price, so it fills **only** orders as good as or better than the AMM; the remainder routes to the AMM.
3. It converges on the optimal split — the size where the marginal CLOB order equals the AMM-effective price for the leftover.
4. **No active pool liquidity → routing is 100% CLOB.** If the CLOB reverts (insufficient token match, price deviation) → the Router skips it, emits a `ClobSkipped(reason)` event, and falls back **entirely to the AMM**.

Users don't need to worry - the Router always returns the best blended price within the same tx.

***

### MEV Protection

PrediX Hook implements **identity commit** to prevent sandwich attacks:

MEV bots cannot frontrun + backrun your trade within the same block - the Hook reverts if the committed identity doesn't match. Opposite-direction swaps from the same identity in one block are rejected; same-direction splits are allowed.

<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

<sub>Anti-sandwich MEV PrediX</sub>
