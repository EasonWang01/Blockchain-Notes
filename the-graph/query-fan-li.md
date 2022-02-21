---
description: 範例
---

# Query 範例

## AAVE APY

{% embed url="https://docs.aave.com/developers/v/2.0/guides/apy-and-apr" %}

[https://api.thegraph.com/subgraphs/name/aave/protocol-v2](https://api.thegraph.com/subgraphs/name/aave/protocol-v2)

```
{
  reserves {
    name
    underlyingAsset
    
    liquidityRate 
    stableBorrowRate
    variableBorrowRate
    
    aEmissionPerSecond
    vEmissionPerSecond
    sEmissionPerSecond
    
    totalATokenSupply
    totalCurrentVariableDebt
  }
}
```

> liquidity 即為 deposit rate 而 stableBorrowRate, variableBorrowRate 為 borrow rate
