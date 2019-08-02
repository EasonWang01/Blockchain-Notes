# Bitfinex API 使用

記得要先過Bitfinex KYC然後去申請一個API金鑰，並選擇適當權限。

![](/assets/螢幕快照 2019-08-02 上午11.56.17.png)

# 監聽 BTC/USD 掛單

```js
const ws = require('ws')
const w = new ws('wss://api.bitfinex.com/ws/2')
const crypto = require('crypto-js')

// 以下填入自己的金鑰資訊
const apiKey = ''
const apiSecret = ''

const authNonce = Date.now() * 1000
const authPayload = 'AUTH' + authNonce
const authSig = crypto
  .HmacSHA384(authPayload, apiSecret)
  .toString(crypto.enc.Hex)

const payload = {
  apiKey,
  authSig,
  authNonce,
  authPayload,
  event: 'auth'
}

w.on('message', (msg) => {
  console.log(msg)
})

let msg = JSON.stringify({
  event: 'subscribe',
  channel: 'ticker',
  symbol: 'tBTCUSD'
})

w.on('open', () => {
  w.send(JSON.stringify(payload))
  w.send(msg)
})  
```



