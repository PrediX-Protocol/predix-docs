# Bots & Mobile

## Programmatic trading

Currently, programmatic trading is done by calling Router contract functions directly via viem (TypeScript) or web3.py (Python). See [Quickstart — TypeScript](quickstart-typescript.md) and [Quickstart — Python](quickstart-python.md).

> **Planned**: API key system and webhook notifications for bot developers are planned for a future release. Currently, all trading goes through on-chain Router contract calls with standard wallet signing.

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
// import Router ABI from source or explorer

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
        chainId = "eip155:1301" // Unichain Sepolia testnet (mainnet: "eip155:130")
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
  bundlerTransport:    http(`${TESTNET_BE_URL}/api/v1/aa/bundler`),
  paymasterTransport:  http(`${TESTNET_BE_URL}/api/v1/aa/paymaster/sponsor`),
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

## Support

* API / bot issues: Discord #api-support or #mobile-dev.
* Bug bounty for bot endpoints: [security@predix.app](mailto:security@predix.app).
* Enterprise: [business@predix.app](mailto:business@predix.app).
