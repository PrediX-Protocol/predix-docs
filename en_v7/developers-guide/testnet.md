# Testnet info

PrediX maintains a testnet deployment on **Unichain Sepolia** for developers to integrate before mainnet launch.

## Network

|                |                                                    |
| -------------- | -------------------------------------------------- |
| **Network**    | Unichain Sepolia                                   |
| **Chain ID**   | `1301`                                             |
| **RPC public** | `https://sepolia.unichain.org`                     |
| **Explorer**   | [sepolia.uniscan.xyz](https://sepolia.uniscan.xyz) |
| **Block time** | \~1s                                               |

Add to MetaMask:

```javascript
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
```

## Faucet

PrediX operates a faucet relayed via the backend:

* **0.0005 ETH** Sepolia (for EOA gas)
* **10,000 test-USDC**
* **Cooldown**: 1 claim / 24 hours / wallet

### How to claim

#### Via app

1. Connect your wallet on `app.testnet.predix.app`.
2. The UI shows a faucet banner -> click **Claim**.
3. Receive tokens within \~5s.

#### Via API

```bash
curl -X POST https://api.testnet.predix.app/v2/faucet \
  -H "Content-Type: application/json" \
  -d '{"address": "0x..."}'
```

Response:

```json
{
  "txHash": "0x...",
  "ethAmount": "0.0005",
  "usdcAmount": "10000",
  "nextClaimAt": 1740186400
}
```

#### Check cooldown

```
GET /v2/faucet/cooldown?address=0x...
```

## API endpoints (testnet)

|             | URL                                           |
| ----------- | --------------------------------------------- |
| Indexer API | TBA — coordinates via Discord #testnet-access |
| Backend API | TBA                                           |
| WebSocket   | TBA                                           |

Schema and endpoint shape are identical to mainnet — switching from testnet to mainnet only requires changing the base URL.

## Contract addresses (testnet)

Latest deploy. Sync via API:

```bash
curl https://api.testnet.predix.app/v2/addresses
```

Response sample:

```json
{
  "diamond": "0x7689E9bf4b2107E2Fd0f1DDA940E2f1143434E39",
  "router": "0x6698253F38F4A4bbBC4A223309B4E560d83D7ee0",
  "exchange": "0xE425698e1835DA0A6086eEB85137A36275993F41",
  "hookProxy": "0x89830AC92Ff936f39C2D11D1fd821c6f977fAAE0",
  "manualOracle": "0x7887f07AF62CE0a4Cf836136135a61b59c36A9d2",
  "paymaster": "0x6bbeeb1255a25e6a57b87d9d88fbe24c3a1ba9e7",
  "faucet": "0x7beD6B3D8397Bc9F77626f84D64BED8894C27350",
  "usdc": "0x5a9153c368946b5b252c32921ebb3c16c692d7d4",
  "poolManager": "0x00b036b58a818b1bc34d502d3fe730db729e62ac",
  "permit2": "0x000000000022D473030F116dDEE9F6B43aC78BA3",
  "deployBlock": 49799033
}
```

## Testnet vs mainnet differences

|                  | Testnet                       | Mainnet                 |
| ---------------- | ----------------------------- | ----------------------- |
| USDC             | Test-USDC from faucet         | Native USDC on Unichain |
| ETH gas          | Sepolia ETH (faucet)          | Real ETH                |
| PRX token        | Not deployed                  | TBA after TGE           |
| Staking          | Disabled                      | Live                    |
| Chainlink oracle | Disabled (no feed on Sepolia) | Live                    |
| Bug bounty       | Limited                       | Full $50k-500k          |
| Persistence      | May reset before mainnet      | Permanent               |

## Testnet limits

* **Faucet rate**: 1 claim / 24h / address.
* **Max test-USDC per address**: \~50,000 USDC (claim multiple times across days).
* **API rate limit**: same as mainnet free tier (60 req/min/IP).

## Development flow

![Dev flow: local dev (anvil fork) -> test integrate testnet -> test edge cases faucet -> self-audit errors -> deploy/connect mainnet](../.gitbook/assets/62-testnet-dev-flow.svg)

### Recommended sequence

1. **Local fork**: `anvil --fork-url https://mainnet.unichain.org` for unit testing.
2. **Testnet integrate**: deploy your bot/app on Sepolia, test for 1-2 weeks.
3. **Edge cases**: trigger failure scenarios (slippage, market pause, oracle revert).
4. **Mainnet**: switch base URL + RPC, smoke test with small amounts.

## Reset notice

The testnet **may be reset** in the following cases:

* Major contract upgrade incompatible with existing storage.
* Pre-mainnet final cleanup.
* Critical bug fix requiring fresh state.

Resets are announced **2 weeks** in advance via Discord + Twitter. Previous state is not migrated; balances reset to 0.

## Testnet bug reports

* Logic / data bugs: Discord #testnet-bugs.
* Security bugs: same channel as mainnet — [security@predix.app](mailto:security@predix.app). Bounty is lower (10-20% of mainnet rate) since testnet holds no real funds, but payouts are still issued.

## Get testnet access

Testnet API endpoints are not fully public to prevent abuse:

1. Join Discord.
2. Channel #testnet-access — share your use case + GitHub.
3. Receive testnet API endpoint + key.

Free for any legitimate development use case (bot, integration test, learning).

## Testnet sunset after mainnet

When mainnet launches:

* Testnet endpoints will remain live for at least 6 months for developers testing mainnet contract changes.
* After 6 months: the testnet may migrate to a new Sepolia instance or be deprecated, with prior notice.

Back up your data beforehand if needed:

* Export portfolio / order history via API.
* Save contract events via RPC `getLogs`.

## Mainnet readiness checklist

Before switching to mainnet, verify:

* [ ] Tested all happy paths on testnet.
* [ ] Tested error handling (slippage, paused, oracle fail).
* [ ] API key scope minimized.
* [ ] Webhook + monitoring setup.
* [ ] Gas estimation buffer.
* [ ] Emergency stop button in bot.
* [ ] Funded mainnet wallet with buffer.
* [ ] Reviewed audit reports + understand risks.
* [ ] Liability + legal compliance for your jurisdiction.
