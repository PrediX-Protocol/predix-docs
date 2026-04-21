---
description: Canonical page cho contract address trên mọi mạng. Always check commit hash + last-updated.
---

# Deployed Addresses

{% hint style="info" %}
Trang này là **canonical source** của contract address. Mọi docs khác link về đây. Nếu bạn copy-paste address, **luôn verify** commit hash `last-updated` dưới đây khớp với repo state.
{% endhint %}

**Last updated**: 2026-04-17 (DEPLOY\_UNICHAIN\_SEPOLIA\_POST\_REMEDIATION.md)
**Source of truth**: `SC/audits/DEPLOY_UNICHAIN_SEPOLIA_POST_REMEDIATION.md`

## Unichain Sepolia Testnet (Chain ID 1301)

| Contract | Address | Role |
|---|---|---|
| **Diamond** | `0xF38a265E6e4F57D000a1CC08004da5B4A380B08A` | Core proxy + facets (Market, Event, Access, Pausable, Cut, Loupe) |
| **PrediXHookV2 (proxy)** | `0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0` | Uniswap v4 Hook proxy (ERC1967 + 48h timelock) |
| **PrediXExchange** | `0xa202abCb2A358c0862B2dA76b553398339F2C638` | CLOB on-chain |
| **PrediXRouter** | `0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7` | Stateless aggregator |
| **ManualOracle** | `0x699A8C74663b1C852E195b2ffa00D5965E992Cf3` | Admin-confirmed oracle adapter |
| **USDC (testnet)** | `0x12fd156C8b5F2901BA2781d97db84AaC56b2b911` | Collateral token (6 decimals) |
| **Uniswap PoolManager** | `0x00B036B58a818B1BC34d502D3fE730Db729e62AC` | v4 core (Unichain Sepolia) |
| **Permit2** | `0x000000000022D473030F116dDEE9F6B43aC78BA3` | Canonical Permit2 |

### Thông tin chain

| Tham số | Giá trị |
|---|---|
| Mạng | Unichain Sepolia (Testnet) |
| Chain ID | `1301` |
| RPC | `https://sepolia.unichain.org` |
| Explorer | `https://sepolia.uniscan.xyz` |
| Faucet | [Faucet Guide](../developers/40-testnet-faucet.md) |

### Explorer links (per contract)

{% tabs %}
{% tab title="Diamond" %}
[sepolia.uniscan.xyz/address/0xF38a265E6e4F57D000a1CC08004da5B4A380B08A](https://sepolia.uniscan.xyz/address/0xF38a265E6e4F57D000a1CC08004da5B4A380B08A)
{% endtab %}

{% tab title="Hook V2" %}
[sepolia.uniscan.xyz/address/0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0](https://sepolia.uniscan.xyz/address/0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0)
{% endtab %}

{% tab title="Exchange" %}
[sepolia.uniscan.xyz/address/0xa202abCb2A358c0862B2dA76b553398339F2C638](https://sepolia.uniscan.xyz/address/0xa202abCb2A358c0862B2dA76b553398339F2C638)
{% endtab %}

{% tab title="Router" %}
[sepolia.uniscan.xyz/address/0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7](https://sepolia.uniscan.xyz/address/0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7)
{% endtab %}
{% endtabs %}

## Unichain Mainnet (Chain ID 130)

{% hint style="warning" %}
**TBA** — Mainnet launch đang chờ external audit hoàn tất. Không có address mainnet nào được công bố chính thức. Bất kỳ contract nào tự xưng là "PrediX mainnet" trước announcement chính thức đều là scam.
{% endhint %}

| Contract | Status |
|---|---|
| Diamond | Chưa deploy |
| Hook V2 | Chưa deploy |
| Exchange | Chưa deploy |
| Router | Chưa deploy |

### Thông tin chain (reference)

| Tham số | Giá trị |
|---|---|
| Mạng | Unichain |
| Chain ID | `130` |
| RPC | `https://mainnet.unichain.org` |
| Explorer | `https://uniscan.xyz` |

## ABIs

ABI của tất cả contract được export tại [github.com/predix-protocol/contracts](https://github.com/predix-protocol) và copy sang [Indexer abis folder](https://github.com/predix-protocol/indexer/tree/main/abis).

Xem [ABIs & Typed Clients](../developers/39-abi-types.md) để biết cách import và generate TypeScript types.

## Verify contract

Contract deploy testnet đã được verify trên explorer. Source code trên repo GitHub khớp với bytecode đã deploy:

```bash
# Verify bằng forge
cd SC/packages/diamond
forge verify-contract 0xF38a265E6e4F57D000a1CC08004da5B4A380B08A Diamond \
  --chain 1301 \
  --etherscan-api-key $UNISCAN_API_KEY
```

## Upgrade notifications

Khi Diamond facet hoặc Hook proxy có upgrade, sự kiện sẽ được emit trên-chain và index:

- `DiamondCut(facets[], init, calldata)` — `DiamondCutFacet`
- `UpgradeProposed(newImpl, proposedAt)` — `PrediXHookProxyV2`
- `Upgraded(newImpl)` — `PrediXHookProxyV2`

Subscribe qua Indexer tables `diamond_cut_history`, `hook_upgrade_history`; hoặc REST: [Backend API — diamond-cuts](../backend-api/05-endpoints-markets.md).

## Responsible disclosure

Nếu phát hiện address trong docs này sai hoặc không khớp source — **không tự action**, mở ticket tại [Bug Bounty](../security/03-bug-bounty.md) hoặc contact `security@predixpro.io`.
