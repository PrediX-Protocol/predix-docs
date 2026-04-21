---
description: Listen blockchain events — viem watchContractEvent, getLogs (backfill), và BE polling patterns.
---

# Event Listening

3 cách receive event data:

1. **On-chain direct subscribe** (viem `watchContractEvent`) — lowest latency (~1 block ≈ 1s)
2. **On-chain backfill** (viem `getLogs`) — historical queries
3. **BE polling** (`GET /api/v2/markets/{id}/activity`) — simpler, higher latency (~2-5s + indexer lag)

## 1. On-chain live subscribe (viem)

### Subscribe single event

```typescript
const unwatch = publicClient.watchContractEvent({
  address: ROUTER,
  abi: RouterAbi,
  eventName: 'Trade',
  onLogs: (logs) => {
    for (const log of logs) {
      console.log('Trade:', {
        marketId: log.args.marketId,
        trader: log.args.trader,
        tradeType: log.args.tradeType,  // 0=BUY_YES, 1=SELL_YES, 2=BUY_NO, 3=SELL_NO
        amountIn: log.args.amountIn,
        amountOut: log.args.amountOut,
        yesPrice: log.args.yesPrice,
      });
    }
  },
  onError: (err) => {
    console.error('Subscribe error:', err);
  },
});

// Later
unwatch();
```

### Filter by indexed params

Event `Trade` có indexed params: `marketId`, `trader`, `recipient`. Filter:

```typescript
publicClient.watchContractEvent({
  address: ROUTER,
  abi: RouterAbi,
  eventName: 'Trade',
  args: {
    marketId: 42n,           // chỉ market 42
    trader: myAddress,        // chỉ trader của mình
  },
  onLogs: handleLogs,
});
```

### Subscribe multiple events cùng lúc

```typescript
// Multiple event names (viem ≥ 2.x)
publicClient.watchEvent({
  address: ROUTER,
  events: RouterAbi.filter(a => a.type === 'event'),
  onLogs: handleLogs,
});
```

Hoặc parallel `watchContractEvent` calls cho mỗi event name.

### Common events to subscribe

| Event | Contract | Use case |
|---|---|---|
| `Trade` | Router | Track all trades (canonical) |
| `OrderPlaced` / `OrderMatched` / `OrderCancelled` | Exchange | MM fill monitoring |
| `PositionSplit` / `PositionMerged` | Diamond | Collateral flow |
| `MarketResolved` | Diamond | Auto-redeem bot trigger |
| `MarketCreated` | Diamond | New market listings |
| `Hook_MarketTraded` | Hook | AMM-specific analytics (note: subset of `Trade`) |
| `UpgradeProposed` | HookProxyV2 | Governance monitor |
| `DiamondCut` | Diamond | Protocol upgrade monitor |

---

## 2. Backfill historical events (getLogs)

Query range events:

```typescript
import { parseAbiItem } from 'viem';

const tradeEvent = parseAbiItem(
  'event Trade(uint256 indexed marketId, address indexed trader, address indexed recipient, uint8 tradeType, uint256 amountIn, uint256 amountOut, uint256 clobFilled, uint256 ammFilled, uint256 yesPrice)'
);

const logs = await publicClient.getLogs({
  address: ROUTER,
  event: tradeEvent,
  fromBlock: 12345000n,
  toBlock: 12345999n,
  args: { marketId: 42n },
});

console.log(`Found ${logs.length} trades in market 42`);
for (const log of logs) {
  console.log(log.args);
}
```

**Limits**: RPC providers thường cap `getLogs` range (Alchemy 10K blocks, public RPC 1K blocks). Chunk nếu cần larger:

```typescript
const CHUNK_SIZE = 1000n;
const startBlock = 12000000n;
const endBlock = 13000000n;

const allLogs = [];
for (let from = startBlock; from <= endBlock; from += CHUNK_SIZE) {
  const to = from + CHUNK_SIZE - 1n;
  const logs = await publicClient.getLogs({ /* ... */ fromBlock: from, toBlock: to });
  allLogs.push(...logs);
  await new Promise(r => setTimeout(r, 100));  // rate limit courtesy
}
```

---

## 3. BE polling (for FE apps)

BE expose activity feed merged từ trades + orders + splits + merges + redeems:

```typescript
// Poll every 5s
setInterval(async () => {
  const { data } = await apiClient.GET('/api/v2/markets/{id}/activity', {
    params: { path: { id: marketId }, query: { since: lastTimestamp } },
  });
  for (const event of data!.data) updateUI(event);
  lastTimestamp = data!.meta.timestamp;
}, 5000);
```

**Trade-off**: Higher latency (~2-5s cache + indexer ~15min finality), nhưng dễ implement, không cần WebSocket, không hit RPC rate limit.

**Bot không nên** dùng BE polling cho fill monitoring — dùng on-chain watch thay.

---

## Parse event log manually

Nếu không dùng `watchContractEvent` abstraction:

```typescript
import { decodeEventLog } from 'viem';

const receipt = await publicClient.getTransactionReceipt({ hash });

for (const log of receipt.logs) {
  try {
    const decoded = decodeEventLog({
      abi: RouterAbi,
      data: log.data,
      topics: log.topics,
    });
    if (decoded.eventName === 'Trade') {
      console.log('Trade event:', decoded.args);
    }
  } catch {
    // Not a Router event; ignore
  }
}
```

---

## WebSocket RPC (low latency)

Most RPC providers expose WebSocket. Setup:

```typescript
import { createPublicClient, webSocket } from 'viem';

const wsClient = createPublicClient({
  chain: unichainSepolia,
  transport: webSocket('wss://sepolia.unichain.org'),
});

wsClient.watchContractEvent({ /* ... */ });
```

WS giảm latency (~200ms) so với HTTP polling. Nhược điểm: có thể drop connection; implement reconnect logic.

---

## Handling reorgs

Events có thể bị re-org (especially close to head). Best practices:

1. **Wait N confirmations** trước khi treat event final:

   ```typescript
   const currentBlock = await publicClient.getBlockNumber();
   const CONFIRMATIONS = 5n;
   if (currentBlock - log.blockNumber >= CONFIRMATIONS) {
     // Treat as final
   }
   ```

2. **Use `finalized` tag** cho high-value decisions:

   ```typescript
   const finalizedBlock = await publicClient.getBlock({ blockTag: 'finalized' });
   if (log.blockNumber <= finalizedBlock.number) {
     // Reorg-safe
   }
   ```

3. **Handle re-emitted events**: viem `watchContractEvent` sẽ emit same event lại nếu chain reorg. Dedup by `txHash:logIndex`.

---

## Example bot — auto-redeem winning positions

```typescript
// Subscribe MarketResolved → check if I have winning tokens → auto-redeem

publicClient.watchContractEvent({
  address: DIAMOND,
  abi: MarketFacetAbi,
  eventName: 'MarketResolved',
  onLogs: async (logs) => {
    for (const log of logs) {
      const { marketId, outcome } = log.args!;
      const market = await getMarketInfo(marketId);
      const winningToken = outcome ? market.yesToken : market.noToken;
      const balance = await readContract({
        address: winningToken, abi: ERC20Abi,
        functionName: 'balanceOf', args: [myAddress],
      });

      if (balance > 0n) {
        console.log(`Auto-redeeming market ${marketId}, balance ${balance}`);
        await walletClient.writeContract({
          address: DIAMOND, abi: MarketFacetAbi,
          functionName: 'redeemMarketTokens',
          args: [marketId],
        });
      }
    }
  },
});
```

Xem [Integration Patterns](42-integration-patterns.md) cho more bot examples.
