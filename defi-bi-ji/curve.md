---
description: https://curve.readthedocs.io/
---

# Curve

Admin fee v.s fee

{% embed url="https://gov.curve.fi/t/discussion-introduction-of-an-admin-fee-to-incentivise-protocol-governance/150" %}

Pools 種類 （Plain pools, Lending pools, Metapools）

{% embed url="https://curve.readthedocs.io/exchange-pools.html" %}

DAO contracts

{% embed url="https://github.com/curvefi/curve-dao-contracts" %}

Gauge

Each pool has an individual liquidity gauge. Curve incentivizes liquidity providers with the CRV. All of the inflation is distributed to Curve liquidity providers, according to measurements taken by the gauges. Gauge 可想像為  staking 對應池子 LP token 的地方。

{% embed url="https://curve.readthedocs.io/dao-gauges.html" %}

獲得 CRV

於 curve 的任意池子提供流動性均會獲得。在 Ypool 可同時獲得 CRV 與 YFI







## Subgraph 相關 Query

[https://thegraph.com/explorer/subgraph?id=0x2382ab6c2099474cf424560a370ed1b1fdb65253-0\&view=Playground](https://thegraph.com/explorer/subgraph?id=0x2382ab6c2099474cf424560a370ed1b1fdb65253-0\&view=Playground)

#### &#x20;LpToken amount

透過 addLiquidityEvents 與 RemoveLiquidityEvent 來做計算獲得 user 當前 lptoken amount

[https://github.com/curvefi/curve-subgraph/issues/5](https://github.com/curvefi/curve-subgraph/issues/5)

```
{
  addLiquidityEvents(
    where: {
      provider: "0xbdfa4f4492dd7b7cf211209c4791af8d52bf5c50",
    },
    orderBy: block,
    orderDirection: desc
  ) 
  {
    block
    tokenAmounts
    timestamp
    transaction
    invariant
    pool {
      name
      assetType
      adminFee
      virtualPrice
      isMeta
      coins {
        token {
          name
        }
      }
    }
  }	
}
```

