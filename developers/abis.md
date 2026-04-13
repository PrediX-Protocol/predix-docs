---
description: ABI references for all PrediX contracts
---

# ABIs

## Diamond ABI (MarketFacet)

Key function signatures:

```json
[
  "function createMarket(string,string,uint256,bytes32,address) returns (bytes32)",
  "function splitPosition(bytes32,uint256)",
  "function mergePositions(bytes32,uint256)",
  "function resolveMarket(bytes32)",
  "function redeemMarketTokens(bytes32)",
  "function getMarket(bytes32) view returns (tuple(string,string,uint256,address,address,bytes32,bytes32,address,bool,bool,uint256,bytes32,uint256))",
  "function isMarketExists(bytes32) view returns (bool)",
  "function getSafetyCaps() view returns (uint256,uint256,uint256)",
  "function groupSplit(bytes32,uint256)",
  "function groupMerge(bytes32,uint256)",
  "function emergencyResolve(bytes32,bool)",
  "function enableRefundMode(bytes32)",
  "function refund(bytes32)"
]
```

## Exchange ABI

```json
[
  "function placeOrder(bytes32,uint8,uint256,uint256) returns (bytes32,uint256)",
  "function cancelOrder(bytes32)",
  "function fillMarketOrder(bytes32,uint8,uint256,uint256,address,address) returns (uint256,uint256)",
  "function getOrder(bytes32) view returns (tuple(address,bytes32,uint8,uint256,uint256,uint256))",
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

## ERC-20 ABI (Outcome Tokens + USDC)

```json
[
  "function balanceOf(address) view returns (uint256)",
  "function totalSupply() view returns (uint256)",
  "function approve(address,uint256) returns (bool)",
  "function transfer(address,uint256) returns (bool)",
  "function allowance(address,address) view returns (uint256)",
  "function permit(address,address,uint256,uint256,uint8,bytes32,bytes32)"
]
```

> ⚠️ Full ABI JSON files are available in the GitHub repository.

---

**Next**: [Create Market](create-market.md) · [Trading Integration](trading-integration.md)
