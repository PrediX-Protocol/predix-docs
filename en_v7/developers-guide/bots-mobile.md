# Bots & mobile

Two different use cases sharing the same infrastructure: API key auth + Router contract.

| Use case                       | Primary stack                                      |
| ------------------------------ | -------------------------------------------------- |
| **Trading bot / market maker** | API key + REST/WebSocket + `@predix/bot-sdk` (TBA) |
| **Mobile app native**          | Swift / Kotlin / RN + WalletConnect + viem         |
| **Web app custom**             | wagmi + viem + RainbowKit                          |

***

## Trading bots

### Register API key

![API key: sign in SIWE -> create key in Settings -> choose scope (read-only/trade/full) -> set rate limit + IP whitelist -> receive key + secret](../.gitbook/assets/61-dev-integration-paths.svg)

1. Sign in to PrediX via SIWE.
2. **Settings -> Developer -> API keys** -> **Create new key**.
3. Choose scope (read-only / trade / full), IP whitelist (optional), expiry (30/90/365 days).
4. Save **API key** + **secret** — the secret will not be shown again.

### Authentication

```
X-API-Key: pk_live_abc123...
X-API-Signature: <HMAC SHA256 of body with secret>
```

Read-only requires only `Authorization: Bearer pk_live_abc123...`.

### Rate limits & tiers

| Tier           | Rate        | Quota       | Concurrent | Cost      |
| -------------- | ----------- | ----------- | ---------- | --------- |
| **Free**       | 60 req/min  | 10k req/day | 5          | $0        |
| **Pro**        | 600 req/min | 1M req/day  | 50         | $20/month |
| **Enterprise** | Custom      | Unlimited   | Custom     | Contact   |

Stake PRX to upgrade tier for free:

* 1k PRX -> 200 req/min
* 10k PRX -> Pro tier free
* 100k PRX -> Enterprise free

### Endpoints

Read endpoints: all Indexer + BE endpoints are available with an API key (see [API reference](api-reference.md)).

**Place order**:

```
POST /api/v1/bots/orders
{
  "marketId": "0x...",
  "side": "BUY_YES",
  "type": "limit",        // limit | market
  "price": "0.45",        // required if limit
  "amount": "100.00",
  "deadline": 1740100000,
  "slippageBps": 50,      // optional, market only
  "clientOrderId": "uuid" // idempotency
}
-> { orderId, txHash, status: "pending" }
```

The API signs and submits via paymaster (sponsored for users eligible through the program). No private key exposure. Scope `trade` is required to place orders.

**Bulk place** (atomic, max 50): `POST /api/v1/bots/orders/bulk`.

**Cancel**: `DELETE /api/v1/bots/orders/:orderId`.

**Position management**:

```
GET    /api/v1/bots/positions
DELETE /api/v1/bots/positions/:id    # close = sell market order
```

### Webhooks

```json
POST /api/v1/webhooks
{
  "url": "https://your-server.com/webhook",
  "events": ["order.filled", "order.cancelled", "market.resolve"],
  "secret": "your-webhook-secret"
}
```

Payload:

```json
{
  "event": "order.filled",
  "timestamp": 1740100000,
  "data": { "orderId": "...", "marketId": "...", "fillPrice": "0.48", "fillAmount": "100.0", "side": "BUY_YES" }
}
```

Verify HMAC:

```typescript
import { createHmac } from 'crypto';
const sig = req.headers['x-predix-signature'];
const expected = createHmac('sha256', WEBHOOK_SECRET).update(req.rawBody).digest('hex');
if (sig !== expected) return res.status(401).end();
```

### Bot examples

**Market maker** around mid price:

```typescript
import { PrediXBot } from '@predix/bot-sdk';

const bot = new PrediXBot({ apiKey: process.env.PREDIX_API_KEY, secret: process.env.PREDIX_SECRET });

async function makeMarket(marketId: string) {
  const orderbook = await bot.getOrderbook(marketId);
  const mid = (orderbook.bestBid + orderbook.bestAsk) / 2;
  await bot.cancelMyOrders(marketId);
  await bot.bulkPlace([
    { marketId, side: 'BUY_YES',  type: 'limit', price: mid - 0.02, amount: '100' },
    { marketId, side: 'SELL_YES', type: 'limit', price: mid + 0.02, amount: '100' },
  ]);
}

setInterval(() => makeMarket('0x...'), 30_000);
```

**Arbitrage** when YES + NO > $1:

```typescript
async function checkArb(marketId: string) {
  const view = await bot.getPriceView(marketId);
  const spread = parseFloat(view.yesPrice) + parseFloat(view.noPrice);
  if (spread > 1.005) {
    await bot.split(marketId, '100');
    await bot.placeMarket(marketId, 'SELL_YES', '100');
    await bot.placeMarket(marketId, 'SELL_NO',  '100');
  }
}
```

### Best practices

* **Idempotency**: every place order includes a unique `clientOrderId` -> replay safe.
* **Retry**: 5xx -> exponential backoff. 429 -> respect `Retry-After`. 4xx -> don't retry.
* **Position size**: cap per-trade at <= 5% of balance. Keep buffer for gas + slippage.
* **Monitor**: log every order + fill. Alert on PnL drop > 10% within 1h.

### Security

* **Never** commit keys to git. Use env vars / secret manager only.
* Rotate keys every 90 days. IP whitelist if using fixed-IP servers.
* Scope minimization: read-only for analytics, trade for bots, full only when withdraw + 2FA is needed.
* Audit: `/api/v1/bots/audit` — review weekly.

### Open-source bot templates

[github.com/predix-protocol/bot-templates](https://github.com/predix-protocol/bot-templates):

* `market-maker/`, `arbitrage/`, `oracle-resolver/`, `lp-manager/` (TS)
* `scanner-py/` (Python)

***

## Mobile / Wagmi integration

### Tech stack support

| Platform     | Recommended                                          |
| ------------ | ---------------------------------------------------- |
| iOS          | Swift + WalletConnect SDK + viem-swift (TBA)         |
| Android      | Kotlin + WalletConnect SDK + ethers-android          |
| React Native | wagmi/connectors + RainbowKit Mobile                 |
| Flutter      | walletconnect\_flutter + custom contract integration |
| Web (custom) | wagmi + viem + RainbowKit                            |

### Web app — Wagmi / RainbowKit

```typescript
import { WagmiConfig } from 'wagmi';
import { unichain } from 'wagmi/chains';
import { RainbowKitProvider, getDefaultConfig } from '@rainbow-me/rainbowkit';

const config = getDefaultConfig({
  appName: 'My PrediX App',
  projectId: 'YOUR_WALLETCONNECT_PROJECT_ID',
  chains: [unichain],
});

function App() {
  return (
    <WagmiConfig config={config}>
      <RainbowKitProvider><YourApp /></RainbowKitProvider>
    </WagmiConfig>
  );
}
```

Trade hook:

```typescript
import { useWriteContract, useWaitForTransactionReceipt } from 'wagmi';
import { routerAbi } from '@predix/abi';

function BuyYesButton({ marketId, usdcIn, minOut }) {
  const { writeContract, data: hash } = useWriteContract();
  const { isLoading } = useWaitForTransactionReceipt({ hash });

  return (
    <button
      onClick={() => writeContract({
        address: ROUTER_ADDRESS,
        abi: routerAbi,
        functionName: 'buyYes',
        args: [marketId, usdcIn, minOut, account, 10n, deadline],
      })}
      disabled={isLoading}
    >
      {isLoading ? 'Confirming...' : 'Buy YES'}
    </button>
  );
}
```

### Mobile native — WalletConnect

**iOS Swift**:

```swift
import WalletConnectSwift

let tx = SwapTx(
    to: routerAddress,
    data: encodeBuyYes(marketId: marketId, usdcIn: 100, minOut: 200, ...),
    value: 0,
    gas: 500_000
)

session.sendTransaction(tx) { result in
    switch result {
    case .success(let txHash): print("Tx: \(txHash)")
    case .failure(let error):  print("Error: \(error)")
    }
}
```

**Android Kotlin**:

```kotlin
import com.walletconnect.sign.client.*

val tx = mapOf(
    "from" to userAddress,
    "to" to routerAddress,
    "data" to encodeBuyYes(...),
    "value" to "0x0",
    "gas" to "0x7A120"
)

SignClient.request(
    Sign.Params.Request(
        sessionTopic = sessionTopic,
        method = "eth_sendTransaction",
        params = Json.encodeToString(tx),
        chainId = "eip155:130"
    )
) { error -> /* handle */ }
```

### React Native — RainbowKit Mobile

```typescript
import { RainbowKitMobileProvider } from '@rainbow-me/rainbowkit-mobile';
import { unichain } from '@wagmi/core/chains';

<RainbowKitMobileProvider chains={[unichain]} walletConnectProjectId="YOUR_ID">
  <App />
</RainbowKitMobileProvider>
```

Same hooks API as web wagmi.

### Account abstraction on mobile

```typescript
import { createKernelClient } from '@zerodev/sdk';
import { createPublicClient, http } from 'viem';
import { unichainSepolia } from 'viem/chains';  // current testnet
// import { unichain } from 'viem/chains';      // mainnet after launch

const publicClient = createPublicClient({
  chain: unichainSepolia,
  transport: http('https://sepolia.unichain.org'),
});

const kernelClient = createKernelClient({
  publicClient,
  bundlerTransport:    http(`${TESTNET_BE_URL}/v2/aa/bundler`),
  paymasterTransport:  http(`${TESTNET_BE_URL}/v2/aa/paymaster/sponsor`),
  validator: passkeyValidator,
});

const userOpHash = await kernelClient.sendUserOperation({
  callData: await kernelClient.encodeCalls([
    { to: USDC,   data: approveCalldata },
    { to: ROUTER, data: buyYesCalldata },
  ]),
});
```

### Passkey native

**iOS** — `ASAuthorizationPlatformPublicKeyCredentialProvider`:

```swift
import AuthenticationServices

let provider = ASAuthorizationPlatformPublicKeyCredentialProvider(
    relyingPartyIdentifier: "predix.app"
)
let request = provider.createCredentialRegistrationRequest(
    challenge: challengeData, name: "user@predix.app", userID: userIdData
)
ASAuthorizationController(authorizationRequests: [request]).performRequests()
```

**Android** — Credential Manager API:

```kotlin
import androidx.credentials.*

val request = CreatePublicKeyCredentialRequest(requestJson = passkeyRequestJson)
credentialManager.createCredentialAsync(request, activity, null, executor, callback)
```

Smart account address is derived from the passkey public key — same address across devices if cloud-synced.

### Performance tips

* **Caching**: wagmi auto-caches with a 60s default. User-specific data (portfolio): 30s. SWR / React Query revalidate-on-focus pattern.
* **RPC efficiency**: Multicall3 (`0xcA11bde05977b3631167028862bE2a173976CA11`) batch reads. WebSocket instead of polling. Indexer API instead of RPC `getLogs` for historical data.
*   **Gas estimation**: add 20% buffer.

    ```typescript
    const estimatedGas = await publicClient.estimateContractGas({...});
    const gasLimit = (estimatedGas * 120n) / 100n;
    ```

### Common errors

| Error                        | Cause                                                             | Fix                                                                 |
| ---------------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------------- |
| `User rejected request`      | User cancelled in wallet                                          | Show retry UI                                                       |
| `Insufficient funds for gas` | EOA lacks ETH, or smart account has no balance + tx not sponsored | Bridge ETH (EOA), or check sponsor eligibility (both account types) |
| `Internal JSON-RPC error`    | RPC down or rate limited                                          | Fallback RPC, retry                                                 |
| `Network mismatch`           | Wallet not connected to Unichain                                  | Auto switch via `wallet_switchEthereumChain`                        |
| `Nonce too low`              | Tx race condition                                                 | Refresh nonce, retry                                                |

### Auto-add Unichain

```typescript
// Testnet — Unichain Sepolia (live)
await window.ethereum.request({
  method: 'wallet_addEthereumChain',
  params: [{
    chainId: '0x515', // 1301 hex
    chainName: 'Unichain Sepolia',
    rpcUrls: ['https://sepolia.unichain.org'],
    blockExplorerUrls: ['https://sepolia.uniscan.xyz'],
    nativeCurrency: { name: 'Ether', symbol: 'ETH', decimals: 18 },
  }],
});

// Mainnet — Unichain (after launch)
// chainId: '0x82' (130), rpcUrls: ['https://mainnet.unichain.org'], explorer: uniscan.xyz
```

### Mobile push notifications

* iOS APNs: register device token with BE via `/api/v1/users/:address/push/ios`.
* Android FCM: `/api/v1/users/:address/push/android`.
* Backend pushes via Firebase / APNs when events match alert criteria.

Setup details: [Notifications](/broken/pages/VjFpu8jlXXqXJ8qF7KcN).

### Open-source examples

* [github.com/predix-protocol/mobile-app-rn](https://github.com/predix-protocol/mobile-app-rn) — React Native reference app.
* [github.com/predix-protocol/ios-example](https://github.com/predix-protocol/ios-example) — Swift native demo.
* [github.com/predix-protocol/android-example](https://github.com/predix-protocol/android-example) — Kotlin native demo.

## Support

* API / bot issues: Discord #api-support or #mobile-dev.
* Bug bounty for bot endpoints: [security@predix.app](mailto:security@predix.app).
* Enterprise: [business@predix.app](mailto:business@predix.app).
