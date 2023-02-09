# CCXT 通用交易所 API

其為一套可在任何交易所進行交易的 API

[https://github.com/ccxt/ccxt/wiki/Manual#market-orders](https://github.com/ccxt/ccxt/wiki/Manual#market-orders)

[https://docs.ccxt.com/en/latest/manual.html](https://docs.ccxt.com/en/latest/manual.html)

## 獲取初始化需要放入參數的 Credentials

> 從 requiredCredentials 可以知道在上方交易所內的 object 參數放入哪些欄位

```javascript
const ccxt = require ('ccxt');

(async function () {
    const exchange = new ccxt.binance ({...})
    console.log(exchange.requiredCredentials)
  },
}) ();
```

## ![](<.gitbook/assets/截圖 2023-02-09 上午11.30.07.png>)

## 獲取可呼叫方法

```javascript
const ccxt = require ('ccxt');

(async function () {
    const exchange = new ccxt.binance ({...})
    console.log(exchange.has)
  },
}) ();
```

## ![](<.gitbook/assets/截圖 2023-02-09 上午11.29.37.png>)

## 獲取所有可用交易對

```javascript
const ccxt = require ('ccxt');

(async function () {
    const exchange = new ccxt.binance ({...})
    console.log(await exchange.loadMarkets ())
  },
}) ();
```

> 最小下單金額可以從 `['TOKEN/TOKEN']['limits']['cost']['min']` 獲取
>
> ```javascript
> const markets = await exchange.loadMarkets ();
> console.log(markets["ETH/USDT"]?.limits?.cost?.min)
> ```
>
> [https://github.com/ccxt/ccxt/issues/1972#issuecomment-366834844](https://github.com/ccxt/ccxt/issues/1972#issuecomment-366834844)

## 建立市價訂單

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
  const order = await ftx.createOrder(symbol, 'market', 'buy', amount);

  console.log (order)
})();
```

## 建立限價訂單

````javascript
const ccxt = require ('ccxt');

const bitgetKey = "";
const bitgetSecret = "";

(async function () {
    const exchange = new ccxt.bitget ({
        apiKey: bitgetKey,
        secret: bitgetSecret,
        password: ""
    })
    const symbol = "ETH/USDT";
    const amount = 0.0065;
    const triggerPrice = 1000;
    const order = await exchange.createOrder(symbol, 'limit', 'buy', amount, triggerPrice);
    console.log('order', order);
}) ();
```
````

## 取得歷史價格

推薦使用[https://min-api.cryptocompare.com/documentation](https://min-api.cryptocompare.com/documentation)

如果要取得歷史資料可以用toTs，會返回其timestamp之前的limit參數筆資料，然後用batch方式去不斷抓資料。

e.g.

```
https://min-api.cryptocompare.com/data/v2/histominute?fsym=BTC&tsym=USD&limit=2000&toTs=1578217560
```

> ![](<.gitbook/assets/螢幕快照 2020-01-05 下午6.11.53.png>)然後將TimeFrom 在放入下一筆的toTs繼續往更早之前的資料抓取
