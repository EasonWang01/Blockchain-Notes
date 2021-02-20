# CCXT 通用交易所 API

其為一套可在任何交易所進行交易的API

[https://github.com/ccxt/ccxt/wiki/Manual\#market-orders](https://github.com/ccxt/ccxt/wiki/Manual#market-orders)

## 創造訂單

```javascript
const ccxt = require("ccxt");
const FTX_API_KEY = process.env.ftx_api_key;
const FTX_API_SECRET = process.env.ftx_api_secret;
(async function() {
  let ftx = new ccxt.ftx({
    apiKey: FTX_API_KEY,
    secret: FTX_API_SECRET
  });
  const symbol = 'BTC/USD'
  const amount = 0.0001 // BTC
  const order = await ftx.createOrder (symbol, 'market', 'buy', amount);

  console.log (order)
})();
```

## 取得歷史價格

推薦使用[https://min-api.cryptocompare.com/documentation](https://min-api.cryptocompare.com/documentation)

如果要取得歷史資料可以用toTs，會返回其timestamp之前的limit參數筆資料，然後用batch方式去不斷抓資料。

e.g.

```text
https://min-api.cryptocompare.com/data/v2/histominute?fsym=BTC&tsym=USD&limit=2000&toTs=1578217560
```

> ![](.gitbook/assets/螢幕快照%202020-01-05%20下午6.11.53.png)然後將TimeFrom 在放入下一筆的toTs繼續往更早之前的資料抓取

