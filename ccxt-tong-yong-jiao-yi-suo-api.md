# CCXT

其為一套可在任何交易所進行交易的API

[https://github.com/ccxt/ccxt/wiki/Manual\#market-orders](https://github.com/ccxt/ccxt/wiki/Manual#market-orders)

```js
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



