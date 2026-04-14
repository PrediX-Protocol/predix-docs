# ABIs

ABI (Application Binary Interface) definitions for all PrediX contracts. For contract addresses, see [Contracts Overview](../contracts/overview.md).

## Diamond ABI (MarketFacet)

Key functions for market interaction:

```json
[
  "function createMarket(string,string,uint256,bytes32,address) returns (bytes32)",
  "function splitPosition(bytes32,uint256)",
  "function mergePositions(bytes32,uint256)",
  "function resolveMarket(bytes32)",
  "function redeemMarketTokens(bytes32)",
  "function getMarket(bytes32) view returns (tuple(string,string,uint256,address,address,bytes32,bytes32,address,bool,bool,uint256,bytes32,uint256))",
  "function isMarketExists(bytes32) view returns (bool)",
  "function getSafetyCaps() view returns (uint256,uint256,uint256)"
]
```

## Exchange ABI

```json
[
  "function placeOrder(bytes32,uint8,uint256,uint256) returns (bytes32,uint256)",
  "function cancelOrder(bytes32)",
  "function getOrder(bytes32) view returns (tuple(bytes32,address,uint8,uint256,uint256,uint256))",
  "function getBestPrices(bytes32) view returns (uint256,uint256,uint256,uint256)",
  "function getDepthAtPrice(bytes32,uint8,uint256) view returns (uint256)",
  "function getOrderBook(bytes32,uint8) view returns (uint256[],uint256[],uint256[],uint256[])"
]
```

## Router ABI

```json
[
  "function buyYes(bytes32,uint256,uint256,address,uint256) returns (uint256)",
  "function sellYes(bytes32,uint256,uint256,address,uint256) returns (uint256)",
  "function buyNo(bytes32,uint256,uint256,uint256,address,uint256) returns (uint256)",
  "function sellNo(bytes32,uint256,uint256,address,uint256) returns (uint256)",
  "function quoteBuyYes(bytes32,uint256) view returns (uint256)",
  "function quoteSellYes(bytes32,uint256) view returns (uint256)",
  "function quoteBuyNo(bytes32,uint256) view returns (uint256)",
  "function quoteSellNo(bytes32,uint256) view returns (uint256)"
]
```

## ERC-20 ABI

Standard ERC-20 + ERC-2612 Permit for outcome tokens and USDC:

```json
[
  "function balanceOf(address) view returns (uint256)",
  "function approve(address,uint256) returns (bool)",
  "function transfer(address,uint256) returns (bool)",
  "function allowance(address,address) view returns (uint256)",
  "function permit(address,address,uint256,uint256,uint8,bytes32,bytes32)"
]
```

## Usage

```typescript
import { ethers } from "ethers";

// Using human-readable ABI (recommended)
const router = new ethers.Contract(
  "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7",
  ["function buyYes(bytes32,uint256,uint256,address,uint256) returns (uint256)"],
  signer
);
```

## Next Steps

- [Developer Quickstart](quickstart.md) — full working examples
- [Contracts Overview](../contracts/overview.md) — addresses and architecture
