---
description: 定義
---

# Schema 定義

subgraph 將索引到的資料存入關連式資料庫中，所以我們需要定義 schema 來讓他知道資料如何儲存。

## 可用型別

![](<../.gitbook/assets/截圖 2022-05-31 下午4.10.01.png>)

[https://thegraph.com/docs/en/developer/create-subgraph-hosted/#built-in-scalar-types](https://thegraph.com/docs/en/developer/create-subgraph-hosted/#built-in-scalar-types)

> 每個 entity 都有一個 id ，類似於 primary key ，只能使用 `Bytes!` 或 `String!`

## schema 範例

每個 entity 要加上 `@entity` 於後方預設都是 mutable 也就是可以在 mapping 邏輯改變數值，如果要固定不可被改動可使用 `@entity(immutable: true)` 下面的 TokenBalance 使用 `token: Token!` 來把 token 結構關聯到 TokenBalance 內，之後可以方便 query

```yaml
type Token @entity(immutable: true) {
  id: Bytes!
}

type TokenBalance @entity {
  id: Bytes!
  amount: Int!
  token: Token!
}
```

## 相互引用

下面範例中  UserTxHistory 引用 Pair

```
type UserTxHistory @entity {
  id: ID!
  address: Bytes!, 
  address1: Bytes!, 
  type: String!, 
  value: BigDecimal!
  pairAddress: Bytes!, 
  pair: Pair!
  txHash: Bytes!
}

type Pair @entity {
  # pair address
  id: ID!
  ....
}  
```

之後於 mapping 檔案可以使用如下將其存入

```javascript
import { Bundle, Token, Pair, UserTxHistory } from '../types/schema' // codegen 後產生

let user = UserTxHistory.load(address.toHexString())
let pair = Pair.load(pairAddress.toHexString())
user.pair = pair.id
user.save()
```

## 一對多引用

TokenBalance 本身納入了 token 結構，但 Token 結構本身也需要用到 TokenBalance 欄位，所以可以在 Token 的 tokenBalances 欄位使用 @derivedFrom，告知他一開始是從 token 引用的

```javascript
type Token @entity(immutable: true) {
  id: Bytes!
  tokenBalances: [TokenBalance!]! @derivedFrom(field: "token")
}

type TokenBalance @entity {
  id: Bytes!
  amount: Int!
  token: Token!
}
```
