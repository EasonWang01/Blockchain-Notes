---
description: 範例
---

# Query 範例

## AAVE APY

{% embed url="https://docs.aave.com/developers/v/2.0/guides/apy-and-apr" %}

{% embed url="https://api.thegraph.com/subgraphs/name/aave/protocol-v2" %}

Market Data

```graphql
{
  reserves {
    name
    symbol
    totalLiquidity
    totalDeposits
	price {
		oracle {
			usdPriceEth
			}
		        priceInEth
		}
		aToken {
			id
			underlyingAssetAddress
		}
	}
}
```

> liquidity 即為 deposit rate 而 stableBorrowRate, variableBorrowRate 為 borrow rate

## Compound APY

```
{
  markets {
    borrowRate
    supplyRate
    symbol
    id
    totalBorrows
    totalSupply
    underlyingAddress
    underlyingName
    underlyingPrice
    underlyingSymbol
    reserveFactor
    underlyingPriceUSD
  }
}
```

## Uniswap

[https://github.com/Uniswap/v2-subgraph/](https://github.com/Uniswap/v2-subgraph/)

[https://thegraph.com/hosted-service/subgraph/easonwang01/uniswap?selected=playground](https://thegraph.com/hosted-service/subgraph/easonwang01/uniswap?selected=playground)

user swap history

```javascript
{
  swaps(where: { from: "0x0Baf7b79F9174c0840aa93a93a2c2A81044A09a2" }) {
    transaction {
      id
      blockNumber
      timestamp
      swaps {
        from
        to
        amountUSD
        amount0In
        amount1In
        amount0Out
        amount1Out
        pair {
          token0 {
            symbol
            name
          }
          token1 {
            symbol
            name
          }
        }
      }
    }
  }
}

```

## 其他範例

> 可 import 以下 json 到 postman 測試

![](<../.gitbook/assets/截圖 2022-03-18 下午3.29.09.png>)

```json
{"info":{"_postman_id":"326ee33e-0b9e-4c3a-9128-13486f4a4d95","name":"Subgraph Research For Assest Dashboard","schema":"https://schema.getpostman.com/json/collection/v2.0.0/collection.json"},"item":[{"name":"AAVE","item":[{"name":"Market Data","id":"0f66824a-21ed-40f1-80b5-695aedd00f62","request":{"method":"POST","header":[],"body":{"mode":"graphql","graphql":{"query":"{\n  reserves {\n    name\n\t\tsymbol\n    totalLiquidity\n    totalDeposits\n\t\tprice {\n\t\t\toracle {\n\t\t\t\tusdPriceEth\n\t\t\t\t}\n\t\t\t\tpriceInEth\n\t\t\t}\n\t\t\taToken {\n\t\t\t\tid\n\t\t\t\tunderlyingAssetAddress\n\t\t\t}\n\t\t}\n}","variables":""}},"url":"https://api.thegraph.com/subgraphs/name/aave/protocol-v2"},"response":[]},{"name":"User Deposit history","id":"11748ea0-b308-475a-b3e3-9f9be46ab7d2","request":{"method":"POST","header":[],"body":{"mode":"graphql","graphql":{"query":"{\n  user(id: \"0x4828d2f2836f02a480340707aaf7fa10c5b822bb\") {\n   depositHistory {\n    id\n    pool {\n      id\n    }\n    reserve {\n      name\n    }\n  }\n }\n}","variables":""}},"url":"https://api.thegraph.com/subgraphs/name/aave/protocol-v2"},"response":[]},{"name":"User aToken balance","id":"7516c72b-651b-4d63-af14-4259522a977d","request":{"method":"POST","header":[],"body":{"mode":"graphql","graphql":{"query":"{\n  user(id: \"0x4828d2f2836f02a480340707aaf7fa10c5b822bb\") {\n   reserves{\n    id\n    currentATokenBalance\n  }\n }\n}","variables":""}},"url":"https://api.thegraph.com/subgraphs/name/aave/protocol-v2"},"response":[]},{"name":"Deposit and borrow rate","id":"a9659538-9921-4675-8419-ec85ae1ff093","request":{"method":"POST","header":[],"body":{"mode":"graphql","graphql":{"query":"{\n  reserves {\n    name\n    underlyingAsset\n    \n    liquidityRate \n    stableBorrowRate\n    variableBorrowRate\n    \n    aEmissionPerSecond\n    vEmissionPerSecond\n    sEmissionPerSecond\n    \n    totalATokenSupply\n    totalCurrentVariableDebt\n  }\n}","variables":""}},"url":"https://api.thegraph.com/subgraphs/name/aave/protocol-v2"},"response":[]}],"id":"85b8d197-3eb9-45c3-b65b-23f469055af5"},{"name":"Compound","item":[{"name":"Market Data","id":"a4b93e06-2b4d-4f4c-8d92-2c402d9a78ae","request":{"method":"POST","header":[],"body":{"mode":"graphql","graphql":{"query":"{\n  markets(first: 100) {\n    borrowRate\n    cash\n    collateralFactor\n    exchangeRate\n    interestRateModelAddress\n    name\n    reserves\n    supplyRate\n    symbol\n    id\n    totalBorrows\n    totalSupply\n    underlyingAddress\n    underlyingName\n    underlyingPrice\n    underlyingSymbol\n    reserveFactor\n    underlyingPriceUSD\n  }\n}\n","variables":""}},"url":"https://api.thegraph.com/subgraphs/name/graphprotocol/compound-v2"},"response":[]},{"name":"User transaction history","id":"3f5147f8-cdfe-41d8-8539-b45e2700e81f","request":{"method":"POST","header":[],"body":{"mode":"graphql","graphql":{"query":"{\n  account(id: \"0x00000000af5a61acaf76190794e3fdf1289288a1\") {\n    tokens {\n      symbol\n      transactions {\n        tx_hash\n      }\n    }\n  }\n}\n","variables":""}},"url":"https://api.thegraph.com/subgraphs/name/graphprotocol/compound-v2"},"response":[]},{"name":"User cToken balance","id":"a671764d-a1d4-4738-9b7e-7644b69008b4","request":{"method":"POST","header":[],"body":{"mode":"graphql","graphql":{"query":"{\n  account(id: \"0x00000000af5a61acaf76190794e3fdf1289288a1\") {\n    id\n    tokens{\n      id\n      symbol\n      cTokenBalance\n    }\n  }\n}\n","variables":""}},"url":"https://api.thegraph.com/subgraphs/name/graphprotocol/compound-v2"},"response":[]},{"name":"Deposit and borrow rate","id":"bf25875e-079a-4912-915c-491e23e810da","request":{"method":"POST","header":[],"body":{"mode":"graphql","graphql":{"query":"{\n  markets {\n    borrowRate\n    supplyRate\n    symbol\n    id\n    totalBorrows\n    totalSupply\n    underlyingAddress\n    underlyingName\n    underlyingPrice\n    underlyingSymbol\n    reserveFactor\n    underlyingPriceUSD\n  }\n}","variables":""}},"url":"https://api.thegraph.com/subgraphs/name/graphprotocol/compound-v2"},"response":[]}],"id":"cb2388ab-84c9-41ba-8e17-02d2732d6196"}]}
```
