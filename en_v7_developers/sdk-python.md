# Python SDK

> **Status: Coming soon.** The official Python SDK is under development. Use web3.py + REST API directly in the meantime.

## Planned

```bash
pip install predix-sdk
```

```python
from predix import PrediXClient

client = PrediXClient(chain="testnet")

# Markets
markets = await client.markets.list(status="active")
market = await client.markets.get(1)

# Trading
quote = await client.trade.quote("buy_yes", market_id=1, usdc_amount=100)
tx = await client.trade.buy_yes(market_id=1, usdc_amount=100, slippage_bps=50)

# Portfolio
positions = await client.portfolio.get("0x...")
```

## Current alternative

Use [web3.py](https://web3py.readthedocs.io/) directly with Router contract:

→ [Quickstart — Python](quickstart-python.md)

→ [API reference](api-reference.md) — REST endpoints via `requests`
