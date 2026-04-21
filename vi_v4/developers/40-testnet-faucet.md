---
description: Chi tiết lấy testnet UNI (gas) + USDC (collateral) để test trên Unichain Sepolia.
---

# Testnet Faucet & Setup

Trước khi test, bạn cần:

1. Ví hỗ trợ EVM (MetaMask, Rabby, WalletConnect)
2. Chain Unichain Sepolia (1301) added
3. Testnet UNI (để trả gas)
4. Testnet USDC (để trade / split)

## 1. Add Unichain Sepolia vào ví

### MetaMask / Rabby manual

| Field | Value |
|---|---|
| Network name | Unichain Sepolia |
| Chain ID | `1301` |
| Currency symbol | UNI |
| RPC URL | `https://sepolia.unichain.org` |
| Block explorer | `https://sepolia.uniscan.xyz` |

### Auto-add via Chainlist

Visit [chainlist.org](https://chainlist.org) → search "Unichain Sepolia" → Add.

### Programmatic (FE / script)

```typescript
await window.ethereum.request({
  method: 'wallet_addEthereumChain',
  params: [{
    chainId: '0x515', // 1301 in hex
    chainName: 'Unichain Sepolia',
    nativeCurrency: { name: 'UNI', symbol: 'UNI', decimals: 18 },
    rpcUrls: ['https://sepolia.unichain.org'],
    blockExplorerUrls: ['https://sepolia.uniscan.xyz'],
  }],
});
```

## 2. Nhận testnet UNI (gas)

Options (pick one):

### Unichain public faucet

[https://faucet.unichain.org](https://faucet.unichain.org)

- Request 0.01 UNI per address per 24h
- Require Twitter verify hoặc GitHub sign-in

### Alchemy / QuickNode faucet

Most Alchemy / QuickNode Sepolia faucets support Unichain Sepolia. Sign up free tier.

### Cross-bridge từ Ethereum Sepolia

1. Nhận Sepolia ETH từ [sepoliafaucet.com](https://sepoliafaucet.com)
2. Bridge qua [Unichain Bridge Testnet](https://bridge-testnet.unichain.org) — Ethereum Sepolia → Unichain Sepolia

**Gas estimate**: 1 lần trade consume ~200K gas. Với 0.01 UNI bạn chạy ~50 trades trước khi hết.

## 3. Nhận testnet USDC (collateral)

### PrediX faucet UI

Visit `https://app-testnet.predixpro.io/faucet`:

1. Connect wallet
2. Request 100 USDC per 24h per address
3. TX confirm trong ~5s

### Contract direct call

```typescript
// PrediX faucet contract
const FAUCET = '0x...'; // TBA - check deployed-addresses

await walletClient.writeContract({
  address: FAUCET,
  abi: FaucetAbi,
  functionName: 'claim',
  args: [],
});
```

Faucet contract có rate limit per address (1 claim / 24h) + max daily pool cap. Nếu pool empty, request faucet refill trên Discord.

## 4. Verify balance

```typescript
import { formatUnits } from 'viem';

const uni = await publicClient.getBalance({ address: account.address });
console.log('UNI:', formatUnits(uni, 18));

const usdc = await publicClient.readContract({
  address: USDC,
  abi: ERC20Abi,
  functionName: 'balanceOf',
  args: [account.address],
});
console.log('USDC:', formatUnits(usdc, 6));
```

## 5. Sanity test trade

Xem [Developer Quickstart](34-developer-quickstart.md) để thử trade đầu tiên.

---

## Troubleshooting

### Faucet nói "Rate limited"

- Chờ 24h
- Dùng address khác
- Ping Discord `#testnet-support` để ops refill cho bạn

### Faucet nói "Faucet empty"

- Ops sẽ top-up mỗi 6 tiếng
- Hoặc Twitter @PrediXPro + tag team nếu urgent

### TX `out of gas`

- Tăng gas limit manual (viem tự estimate nhưng có thể under-estimate cho complex route)
- Check `publicClient.estimateGas(tx)` trước khi submit

### `insufficient allowance`

- Approve USDC trước mỗi trade đầu tiên
- Best practice: `approve(spender, MaxUint256)` một lần + trade unlimited

```typescript
import { maxUint256 } from 'viem';

await walletClient.writeContract({
  address: USDC,
  abi: ERC20Abi,
  functionName: 'approve',
  args: [ROUTER, maxUint256],
});
```

### `Market_NotFound`

- marketId bạn dùng không tồn tại — `GET /api/v2/markets` để list valid IDs
- Hoặc market đã close và cleaned up

### `Router_InvalidMarket`

- Market tồn tại nhưng Hook pool chưa register — edge case, report tới team

---

## Reset / clean workspace

```bash
# Remove localStorage auth tokens
localStorage.removeItem('predix:sessionToken');

# Disconnect wallet từ app
# Settings → Disconnect wallet
```

## Test wallet (disposable)

Cho development, sinh keypair mới thay vì dùng production key:

```bash
# viem CLI
npx viem generate-private-key

# hoặc
openssl rand -hex 32 | xxd -r -p | xxd -i -c 32
```

**NEVER** dùng mainnet key cho testing. Testnet wallet lưu trong `.env.local`, gitignored.

## Reference URLs

| Resource | URL |
|---|---|
| Explorer | [sepolia.uniscan.xyz](https://sepolia.uniscan.xyz) |
| Public RPC | `https://sepolia.unichain.org` |
| Backup RPCs | QuickNode, Alchemy, Chainstack (free tier) |
| PrediX app testnet | [app-testnet.predixpro.io](https://app-testnet.predixpro.io) |
| PrediX docs | [docs.predixpro.io](https://docs.predixpro.io) |
| Discord `#testnet-support` | [discord.gg/predixpro](https://discord.gg/predixpro) |
