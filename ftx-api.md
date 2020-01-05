# FTX API

[https://docs.ftx.com/?csharp\#rest-api](https://docs.ftx.com/?csharp#rest-api)

## Authentication {#authentication}

只要是需要認證的API，都必須要帶有以下Header: `FTX-KEY, FTX-SIGN, FTX-TS`

#### GET /account

[https://docs.ftx.com/?javascript\#account](https://docs.ftx.com/?javascript#account)

```js
const crypto = require("crypto");
const https = require("https");
const FTX_API_KEY = process.env.ftx_api_key;
const FTX_API_SECRET = process.env.ftx_api_secret;

const hostname = "ftx.com";
const endpoint = "/api/account";
const method = "GET";
const nonce = Date.now();

const hmac = crypto.createHmac("sha256", FTX_API_SECRET);
const signature_payload = `${nonce}${method}${endpoint}`;
hmac.update(signature_payload);
const digestHex = hmac.digest("hex");

const options = {
  host: hostname,
  port: 443,
  path: endpoint,
  method,
  headers: {
    "FTX-KEY": FTX_API_KEY,
    "FTX-SIGN": digestHex,
    "FTX-TS": nonce.toString()
  }
};

var req = https.request(options, function(res) {
  res.on("data", function(data) {
    console.log(data.toString());
  });
});

req.end();
```

#### POST /orders



