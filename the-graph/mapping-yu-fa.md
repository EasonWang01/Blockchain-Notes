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

## 呼叫合約的 function

[https://thegraph.com/docs/en/developer/assemblyscript-api/#access-to-smart-contract-state](https://thegraph.com/docs/en/developer/assemblyscript-api/#access-to-smart-contract-state)

```javascript
let tokenContract = TokenContract.bind(event.address);
token.contentURI = tokenContract.tokenURI(event.params.tokenId);
```

## Schema 內含 schema

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

## 要用 null 判斷 entity 是否存過，不能用 or

以下會出現錯誤：

> 設定屬性不能用 or 要直接寫在 === null 判斷內。
>
> 另外如果變數沒用到還是要註解，不然仍會編譯進去產生錯誤

```javascript
 let userPairBalance = UserPairBalance.load(userPairBalanceId)
 let userPairBalanceCurrent = userPairBalance.balance || BigDecimal.fromString('0')
 if (userPairBalance !== null) {
    userPairBalance = new UserPairBalance(userPairBalanceId)
    if(type === "fromUser") {
      userPairBalance.balance = userPairBalanceCurrent.minus(value)
    }
    if(type === "toUser") {
      userPairBalance.balance = userPairBalanceCurrent.plus(value)
    }
 }   
```

正確用法：

```javascript
 let userPairBalance = UserPairBalance.load(userPairBalanceId)
 if (userPairBalance !== null) {
    if(type === "fromUser") {
      userPairBalance.balance = userPairBalance.balance.minus(value)
    }
    if(type === "toUser") {
      userPairBalance.balance = userPairBalance.balance.plus(value)
    }
  }
if (userPairBalance === null) {
  userPairBalance = new UserPairBalance(userPairBalanceId)
  userPairBalance.balance = value
}
```
