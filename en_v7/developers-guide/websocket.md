# WebSocket

Real-time streaming for orderbook updates, price changes, trades, and user portfolio events.

![WebSocket flow: Client connects → subscribes to topic (orderbook, trades) → server confirms → pushes data updates → ping/pong keepalive](assets/websocket-flow.svg)

## Connection

```
Endpoint: wss://<host>/ws/realtime
Auth (optional): wss://<host>/ws/realtime?token=<session_token>
```

Authenticated connection required for user-specific topics (positions, orders, portfolio).

## Subscribe / Unsubscribe

```json
// Subscribe to market orderbook
{ "type": "subscribe", "topic": "orderbook", "marketId": "1" }

// Subscribe to user positions (requires auth)
{ "type": "subscribe", "topic": "user-positions", "subject": "0xYourAddress" }

// Unsubscribe
{ "type": "unsubscribe", "topic": "orderbook", "marketId": "1" }
```

## Topics

### Market topics (public)

Require `marketId` parameter.

| Topic | Data | Description |
|---|---|---|
| `orderbook` | Bid/ask levels | Real-time orderbook depth updates |
| `prices` | Latest YES/NO price | Price tick on every trade |
| `trades` | Trade events | New trades as they happen |

### User topics (authenticated)

Require `subject` = user wallet address. Must match authenticated session.

| Topic | Data | Description |
|---|---|---|
| `user-trades` | User's trades | When your orders fill |
| `user-orders` | Order status | Place, fill, cancel events |
| `user-positions` | Position changes | Balance updates |
| `user-portfolio` | Portfolio value | Aggregate P&L updates |

## Message format

### Server → Client

```json
// Subscription confirmed
{ "type": "subscribed", "topic": "orderbook", "marketId": "1" }

// Data push
{ "type": "data", "topic": "orderbook", "marketId": "1", "data": { ... } }

// Ping (client must respond with pong)
{ "type": "ping" }

// Error
{ "type": "error", "message": "Invalid topic" }
```

### Client → Server

```json
{ "type": "pong" }
```

## Limits

| Limit | Value |
|---|---|
| Subscriptions per connection | 50 |
| Connections per IP | 20 |
| Global connections | 5,000 |

## Example — TypeScript

```typescript
const ws = new WebSocket('wss://api.predix.app/ws/realtime');

ws.onopen = () => {
  // Subscribe to market orderbook
  ws.send(JSON.stringify({
    type: 'subscribe',
    topic: 'orderbook',
    marketId: '1',
  }));

  // Subscribe to market trades
  ws.send(JSON.stringify({
    type: 'subscribe',
    topic: 'trades',
    marketId: '1',
  }));
};

ws.onmessage = (event) => {
  const msg = JSON.parse(event.data);

  if (msg.type === 'ping') {
    ws.send(JSON.stringify({ type: 'pong' }));
    return;
  }

  if (msg.type === 'data') {
    console.log(`[${msg.topic}]`, msg.data);
  }
};
```

## Example — Python

```python
import asyncio
import json
import websockets

async def listen():
    uri = "wss://api.predix.app/ws/realtime"
    async with websockets.connect(uri) as ws:
        # Subscribe
        await ws.send(json.dumps({
            "type": "subscribe",
            "topic": "prices",
            "marketId": "1",
        }))

        async for message in ws:
            msg = json.loads(message)
            if msg["type"] == "ping":
                await ws.send(json.dumps({"type": "pong"}))
            elif msg["type"] == "data":
                print(f"[{msg['topic']}]", msg["data"])

asyncio.run(listen())
```

## Authentication for user topics

```typescript
// Get session token via SIWE
const challengeRes = await fetch('/api/v1/auth/challenge');
const { nonce } = await challengeRes.json();

const signature = await wallet.signMessage({ message: siweMessage(nonce) });

const verifyRes = await fetch('/api/v1/auth/verify', {
  method: 'POST',
  body: JSON.stringify({ address, signature }),
});
const { token } = await verifyRes.json();

// Connect with auth
const ws = new WebSocket(`wss://api.predix.app/ws/realtime?token=${token}`);
```
