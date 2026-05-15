# Quickstart — Python

Buy YES tokens on a PrediX market using web3.py.

## Prerequisites

```bash
pip install web3 requests
```

You need:
- A wallet private key with ETH on Unichain Sepolia (for gas)
- USDC on Unichain Sepolia (get from [Faucet](testnet.md))

## Complete working example

```python
"""
PrediX — Python Quickstart
Buy YES tokens on a prediction market via Router contract.
"""
import json
import time
from web3 import Web3

# --- Config ---
RPC_URL = "https://sepolia.unichain.org"
PRIVATE_KEY = "0x..."  # your private key
CHAIN_ID = 1301  # Unichain Sepolia

# Contract addresses (staging)
ROUTER = "0x1267723f500C0437295698d36d521bd060Bed0EB"
USDC = "0x5a9153c368946B5b252c32921EbB3c16c692D7D4"
PERMIT2 = "0x000000000022D473030F116dDEE9F6B43aC78BA3"

# --- Setup ---
w3 = Web3(Web3.HTTPProvider(RPC_URL))
account = w3.eth.account.from_key(PRIVATE_KEY)
print(f"Wallet: {account.address}")

# --- ABI (minimal) ---
ERC20_ABI = json.loads('[{"name":"approve","type":"function","inputs":[{"name":"spender","type":"address"},{"name":"amount","type":"uint256"}],"outputs":[{"name":"","type":"bool"}]}]')

ROUTER_ABI = json.loads("""[
  {
    "name": "buyYes",
    "type": "function",
    "stateMutability": "nonpayable",
    "inputs": [
      {"name": "marketId", "type": "uint256"},
      {"name": "usdcIn", "type": "uint256"},
      {"name": "minYesOut", "type": "uint256"},
      {"name": "recipient", "type": "address"},
      {"name": "maxFills", "type": "uint256"},
      {"name": "deadline", "type": "uint256"}
    ],
    "outputs": [
      {"name": "yesOut", "type": "uint256"},
      {"name": "clobFilled", "type": "uint256"},
      {"name": "ammFilled", "type": "uint256"}
    ]
  },
  {
    "name": "quoteBuyYes",
    "type": "function",
    "stateMutability": "nonpayable",
    "inputs": [
      {"name": "marketId", "type": "uint256"},
      {"name": "usdcIn", "type": "uint256"},
      {"name": "maxFills", "type": "uint256"}
    ],
    "outputs": [
      {"name": "expectedYesOut", "type": "uint256"},
      {"name": "clobPortion", "type": "uint256"},
      {"name": "ammPortion", "type": "uint256"}
    ]
  }
]""")

usdc = w3.eth.contract(address=USDC, abi=ERC20_ABI)
router = w3.eth.contract(address=ROUTER, abi=ROUTER_ABI)


def approve_usdc():
    """One-time: approve USDC to Permit2."""
    tx = usdc.functions.approve(
        PERMIT2, 10**12  # 1M USDC
    ).build_transaction({
        "from": account.address,
        "nonce": w3.eth.get_transaction_count(account.address),
        "chainId": CHAIN_ID,
    })
    signed = account.sign_transaction(tx)
    tx_hash = w3.eth.send_raw_transaction(signed.raw_transaction)
    receipt = w3.eth.wait_for_transaction_receipt(tx_hash)
    print(f"Approved USDC: {receipt.transactionHash.hex()}")


def quote_buy_yes(market_id: int, usdc_amount: int) -> int:
    """Get expected YES output (non-view — uses eth_call)."""
    result = router.functions.quoteBuyYes(
        market_id, usdc_amount, 10
    ).call({"from": account.address})
    expected_out, clob, amm = result
    print(f"Quote: {expected_out} YES (CLOB: {clob}, AMM: {amm})")
    return expected_out


def buy_yes(market_id: int, usdc_amount: int, min_out: int):
    """Execute buyYes trade."""
    deadline = int(time.time()) + 300  # 5 min
    tx = router.functions.buyYes(
        market_id,
        usdc_amount,
        min_out,
        account.address,
        10,  # maxFills
        deadline,
    ).build_transaction({
        "from": account.address,
        "nonce": w3.eth.get_transaction_count(account.address),
        "chainId": CHAIN_ID,
    })
    signed = account.sign_transaction(tx)
    tx_hash = w3.eth.send_raw_transaction(signed.raw_transaction)
    receipt = w3.eth.wait_for_transaction_receipt(tx_hash)
    print(f"Trade complete: {receipt.transactionHash.hex()}")
    return receipt


if __name__ == "__main__":
    # Step 1: Approve (run once)
    # approve_usdc()

    # Step 2: Quote
    market_id = 1
    usdc_in = 100 * 10**6  # 100 USDC (6 decimals)
    expected = quote_buy_yes(market_id, usdc_in)

    # Step 3: Execute with 0.5% slippage
    min_out = expected * 995 // 1000
    buy_yes(market_id, usdc_in, min_out)
```

## Run

```bash
export PRIVATE_KEY="0x..."
python quickstart.py
```

## Key notes

- **Price precision**: `1e6` = 100%. Prices range `$0.01–$0.99`.
- **USDC**: 6 decimals. `100 * 10**6 = 100 USDC`.
- **Quote functions** are NOT `view` — they must be called via `eth_call` (`.call()` in web3.py). They return `(0, 0, 0)` on invalid market state, never revert.
- **marketId** is `uint256` (not bytes32).
- **maxFills**: how many CLOB orders to try matching. `10` is a good default.
- **Routing**: Router tries CLOB first, then AMM (Uniswap v4) for remainder. Unused input refunded automatically.

## Exchange functions (CLOB direct)

For advanced users who want to interact with the CLOB directly:

```python
# Place limit order
# placeOrder(marketId, side, price, amount, builder)
# side: 0=BUY_YES, 1=SELL_YES, 2=BUY_NO, 3=SELL_NO
# price: 1e6 = 100%, e.g. 450000 = $0.45

# Cancel order
# cancelOrder(orderId)  — orderId is bytes32

# View orderbook
# getOrderBook(marketId, depth) → yesBids, yesAsks, noBids, noAsks
# getBestPrices(marketId) → bestBidYes, bestAskYes, bestBidNo, bestAskNo
```

See [Router integration](router-integration.md) for detailed Exchange API.

## Next steps

- [Router integration](router-integration.md) — Permit2, batch trading, CLOB direct
- [API reference](api-reference.md) — REST API for market data
- [WebSocket](websocket.md) — real-time streaming
