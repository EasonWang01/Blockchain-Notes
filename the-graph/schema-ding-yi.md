---
description: 定義
---

# Schema 定義

### 相互引用

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

之後再 mapping 可以使用如下將其存入

```javascript
import { Bundle, Token, Pair, UserTxHistory } from '../types/schema' // codegen 後產生

let user = UserTxHistory.load(address.toHexString())
let pair = Pair.load(pairAddress.toHexString())
user.pair = pair.id
user.save()
```
