---
description: 語法
---

# mapping 語法

## 判斷是否存過資料

> 避免覆蓋原資料

```javascript
  let user = User.load(userId)
  if (user === null) {
    user = new User(userId)
  }
```

## 獲取區塊時間

```javascript
event.block.timestamp.toI32()
```

schema

```
timestamp: Int!
```

## 數字運算

```javascript
userPairBalance.balance.minus(value)
userPairBalance.balance.plus(value)
```

schema

```
balance: BigDecimal!
```

{% embed url="https://thegraph.com/docs/en/developer/assemblyscript-api#big-decimal" %}

## Schema 內含 scheam

```javascript
let pair = Pair.load(pairAddress.toHexString())
user.pair = pair.id
```

schema

```
type UserPairBalance @entity {
  id: ID!
  pair: Pair!
}

type Pair @entity {
 ...
}
```
