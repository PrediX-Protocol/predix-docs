---
description: Make your first trade on PrediX in minutes.
---

# Quick start

### First Trade

> Learn how to access PrediX, fund your account, and begin trading predictive markets on Unichain.

{% stepper %}
{% step %}
#### <mark style="color:$danger;">Connect Wallet</mark>

Sign in using:

* [x] Passkey authentication (biometric, Web2-style onboarding)
* [x] MetaMask
* [x] Rainbow
* [x] WalletConnect

_Eligible users may receive sponsored transactions through paymaster infrastructure, reducing onboarding friction for first-time users._

{% hint style="info" %}
[How to connect wallet?](first-trade/connect-wallet.md)
{% endhint %}
{% endstep %}

{% step %}
#### <mark style="color:$danger;">Bridge USDC (optional)</mark>

If your USDC is on another network, bridge assets to Unichain before trading.

PrediX supports simplified USDC onboarding through Circle CCTP infrastructure and direct transfers from centralized exchanges.

{% hint style="info" %}
[How to bridge to Unichain?](first-trade/bridge.md)
{% endhint %}
{% endstep %}

{% step %}
#### <mark style="color:$danger;">Start Trading</mark>

Browse prediction markets and trade YES or NO outcome tokens in realtime.

PrediX supports:

* market order execution,
* limit orders through on-chain CLOB infrastructure,
* and realtime portfolio & PnL tracking.

{% hint style="info" %}
Learn more about:&#x20;

* [x] [Market Order](first-trade/market-order.md)
* [x] [Limit Order (CLOB)](first-trade/limit-order.md)
{% endhint %}
{% endstep %}

{% step %}
#### <mark style="color:$danger;">Track Your Positions</mark>

Monitor open positions, realized PnL, settlement history, and market activity in realtime through the portfolio dashboard.

{% hint style="info" %}
[How to view Portfolio & P\&L?](first-trade/portfolio.md)
{% endhint %}
{% endstep %}
{% endstepper %}

***

### Earn PRX Points

Every interaction on PrediX contributes to protocol Points, which convert into PRX allocations at TGE.

Selected campaigns and seasonal events may receive boosted multipliers, including ecosystem activations and major global event markets.

{% content-ref url="../economics/points.md" %}
[points.md](../economics/points.md)
{% endcontent-ref %}

***

### Network Information

{% hint style="info" %}
**You are currently on TESTNET BETA**.&#x20;

All transactions on testnet have no real value — balances and history will be reset before mainnet launch. Testnet activity is tracked via the points campaign and converts to PRX at TGE.
{% endhint %}

<table><thead><tr><th width="229.66668701171875">Information</th><th>Testnet (live now)</th><th>Mainnet (coming soon)</th></tr></thead><tbody><tr><td><strong>Network</strong></td><td>Unichain Sepolia</td><td>Unichain</td></tr><tr><td><strong>Chain ID</strong></td><td><code>1301</code></td><td><code>130</code></td></tr><tr><td><strong>Public RPC</strong></td><td><code>https://sepolia.unichain.org</code></td><td><code>https://mainnet.unichain.org</code></td></tr><tr><td><strong>Explorer</strong></td><td><a href="https://sepolia.uniscan.xyz/">https://sepolia.uniscan.xyz/</a></td><td><a href="https://uniscan.xyz/">https://uniscan.xyz/</a></td></tr><tr><td><strong>USDC</strong></td><td><code>0x2D56777Af1B52034068Af6864741a161dEE613Ac</code> (test-USDC)</td><td>TBA</td></tr><tr><td><strong>Faucet</strong></td><td>Yes — 0.0005 ETH + 10k USDC / 24h / wallet</td><td>N/A</td></tr><tr><td><strong>Status</strong></td><td>✅ Beta live for users + devs</td><td>🟡 TBA — after external audit</td></tr></tbody></table>



> **Tip**: The app automatically adds the network to your wallet on first connection. No manual setup needed.

{% content-ref url="../developers-guide/testnet.md" %}
[testnet.md](../developers-guide/testnet.md)
{% endcontent-ref %}

### Task-based tutorials

| I want to...                               | Go to                                                           |
| ------------------------------------------ | --------------------------------------------------------------- |
| Buy / sell at market price                 | [Market order](first-trade/market-order.md)                     |
| Place a price and wait for a fill          | [Limit order (CLOB)](first-trade/limit-order.md)                |
| Provide liquidity, earn fees               | [Liquidity provider](liquidity-and-market/provide-liquidity.md) |
| Create a new market                        | [Create market](liquidity-and-market/create-market.md)          |
| View positions, P\&L, history              | [Portfolio & P\&L](first-trade/portfolio.md)                    |
| Read charts, change timeframe              | [Chart & timeframe](features/chart-timeframe.md)                |
| Claim USDC after resolution / get a refund | [Redeem & refund](first-trade/redeem-refund.md)                 |
| Trade in a multi-outcome event             | [Multi-outcome event](features/multi-outcome.md)                |
| Discover top traders, copy trading         | [Leaderboard & traders](features/leaderboard.md)                |
| Set up notifications + price alerts        | [Notifications & price alerts](features/notifications.md)       |
| Comment + social feed in-app               | [Discussion & social feed](features/discussion.md)              |

Need to understand the mechanics underneath? See [Concepts](../core-concepts/concepts.md).

Looking for API integration? See [Developers](../developers-guide/developers.md).
