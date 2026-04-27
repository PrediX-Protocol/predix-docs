# Mobile / Wagmi integration

Build mobile app native (iOS / Android) hoặc tích hợp Wagmi / RainbowKit cho web app riêng.

## Tech stack support

| Platform | Recommended |
|---|---|
| iOS | Swift + WalletConnect SDK + viem-swift (TBA) |
| Android | Kotlin + WalletConnect SDK + ethers-android |
| React Native | wagmi/connectors + RainbowKit Mobile |
| Flutter | walletconnect_flutter + custom contract integration |
| Web (custom) | wagmi + viem + RainbowKit |

## Web app — Wagmi / RainbowKit

```typescript
import { WagmiConfig, createConfig } from 'wagmi';
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
      <RainbowKitProvider>
        <YourApp />
      </RainbowKitProvider>
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
  const { isLoading, isSuccess } = useWaitForTransactionReceipt({ hash });

  const handleClick = () => {
    writeContract({
      address: ROUTER_ADDRESS,
      abi: routerAbi,
      functionName: 'buyYes',
      args: [marketId, usdcIn, minOut, account, 10n, deadline],
    });
  };

  return (
    <button onClick={handleClick} disabled={isLoading}>
      {isLoading ? 'Confirming...' : 'Buy YES'}
    </button>
  );
}
```

## Mobile native — WalletConnect

### iOS Swift

```swift
import WalletConnectSwift

let session = WalletConnect.session
let routerAddress = "0x..."
let marketId = "0x..."

let tx = SwapTx(
    to: routerAddress,
    data: encodeBuyYes(marketId: marketId, usdcIn: 100, minOut: 200, ...),
    value: 0,
    gas: 500_000
)

session.sendTransaction(tx) { result in
    switch result {
    case .success(let txHash):
        print("Tx submitted: \(txHash)")
    case .failure(let error):
        print("Error: \(error)")
    }
}
```

### Android Kotlin

```kotlin
import com.walletconnect.sign.client.*

val routerAddress = "0x..."
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

## React Native — RainbowKit Mobile

```typescript
import { RainbowKitMobileProvider } from '@rainbow-me/rainbowkit-mobile';
import { unichain } from '@wagmi/core/chains';

<RainbowKitMobileProvider
  chains={[unichain]}
  walletConnectProjectId="YOUR_ID"
>
  <App />
</RainbowKitMobileProvider>
```

Same hooks API như web wagmi.

## Account abstraction trên mobile

Smart account + paymaster support full trên mobile:

```typescript
import { createKernelClient } from '@zerodev/sdk';
import { createPublicClient, http } from 'viem';
import { unichainSepolia } from 'viem/chains';  // testnet hiện tại
// import { unichain } from 'viem/chains';      // mainnet sau launch

const publicClient = createPublicClient({
  chain: unichainSepolia,
  transport: http('https://sepolia.unichain.org'),
});

const kernelClient = createKernelClient({
  publicClient,
  // Testnet BE proxy URLs (gated — xem Testnet info)
  // Mainnet sau launch: https://api.predix.app/v2/aa/{bundler,paymaster}
  bundlerTransport: http(`${TESTNET_BE_URL}/v2/aa/bundler`),
  paymasterTransport: http(`${TESTNET_BE_URL}/v2/aa/paymaster/sponsor`),
  validator: passkeyValidator,  // chỉ passkey hỗ trợ AA
});

// Send UserOp
const userOpHash = await kernelClient.sendUserOperation({
  callData: await kernelClient.encodeCalls([
    { to: USDC, data: approveCalldata },
    { to: ROUTER, data: buyYesCalldata },
  ]),
});
```

## Passkey trên mobile

iOS, Android có native passkey API:

### iOS — ASAuthorizationPlatformPublicKeyCredentialProvider

```swift
import AuthenticationServices

let provider = ASAuthorizationPlatformPublicKeyCredentialProvider(
    relyingPartyIdentifier: "predix.app"
)
let request = provider.createCredentialRegistrationRequest(
    challenge: challengeData,
    name: "user@predix.app",
    userID: userIdData
)
let controller = ASAuthorizationController(authorizationRequests: [request])
controller.delegate = self
controller.performRequests()
```

### Android — Credential Manager API

```kotlin
import androidx.credentials.*

val request = CreatePublicKeyCredentialRequest(
    requestJson = passkeyRequestJson
)

credentialManager.createCredentialAsync(
    request,
    activity,
    null,
    Executors.newSingleThreadExecutor(),
    object : CredentialManagerCallback<CreateCredentialResponse, CreateCredentialException> {
        override fun onResult(result: CreateCredentialResponse) {
            // Use result
        }
    }
)
```

Smart account address derive từ passkey public key — same address giữa các thiết bị nếu cloud sync.

## SDK — `@predix/sdk` (TBA)

```typescript
import { PrediXClient } from '@predix/sdk';

const client = new PrediXClient({
  chain: 'mainnet',
  walletAdapter: yourWalletConnector,
});

// High-level API
const markets = await client.markets.list({ category: 'crypto' });
const tx = await client.trade.buyYes(marketId, '100', { slippage: 50 });
const portfolio = await client.user.portfolio();
const stake = await client.staking.stake('1000');
```

SDK ngắn hơn so với direct viem call. Roadmap: TS + Python + Rust.

## Performance tips

### Caching

- Wagmi auto-cache call results 60s default.
- Custom cache for user-specific data (portfolio): 30s.
- Use SWR / React Query revalidate-on-focus pattern.

### RPC efficiency

- Batch reads via Multicall3 (canonical address `0xcA11bde05977b3631167028862bE2a173976CA11`).
- Subscribe via WebSocket thay vì poll.
- Use Indexer API thay vì RPC `getLogs` cho historical (rate limit RPC).

### Gas estimation

```typescript
const estimatedGas = await publicClient.estimateContractGas({
  address: ROUTER,
  abi: routerAbi,
  functionName: 'buyYes',
  args: [...],
  account,
});

// Add 20% buffer
const gasLimit = (estimatedGas * 120n) / 100n;
```

## Common errors

| Error | Cause | Fix |
|---|---|---|
| `User rejected request` | User cancel ví | Show retry UI |
| `Insufficient funds for gas` | EOA wallet thiếu ETH, hoặc smart account không có balance + tx không được sponsor cover | Bridge ETH (EOA), hoặc check sponsor eligibility (cả 2 account types đều có thể nhận sponsor nếu đủ điều kiện) |
| `Internal JSON-RPC error` | RPC down hoặc rate limit | Fallback RPC, retry |
| `Network mismatch` | Ví không connect Unichain | Auto switch via `wallet_switchEthereumChain` |
| `Nonce too low` | Tx race condition | Refresh nonce, retry |

## Auto-add Unichain

Nếu user wallet chưa có Unichain network:

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

// Mainnet — Unichain (sau launch)
// chainId: '0x82' (130), chainName: 'Unichain'
// rpcUrls: ['https://mainnet.unichain.org']
// blockExplorerUrls: ['https://uniscan.xyz']
```

App PrediX UI chính tự handle. Nếu integrate riêng, code snippet này useful.

## Mobile push notifications

Subscribe push:
- iOS APNs: register device token vào BE qua `/api/v2/users/:address/push/ios`.
- Android FCM: `/api/v2/users/:address/push/android`.

Backend push qua Firebase / APNs khi event match alert criteria.

Setup detail: [Notifications](../cong-dong/notifications.md).

## Examples open-source

- [github.com/predix-protocol/mobile-app-rn](https://github.com/predix-protocol/mobile-app-rn) — React Native reference app.
- [github.com/predix-protocol/ios-example](https://github.com/predix-protocol/ios-example) — Swift native demo.
- [github.com/predix-protocol/android-example](https://github.com/predix-protocol/android-example) — Kotlin native demo.

## Support

- Mobile dev question: Discord #mobile-dev.
- WalletConnect issue: [walletconnect.com/support](https://walletconnect.com/support).
- Enterprise mobile partnership: [business@predix.app](mailto:business@predix.app).
