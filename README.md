# \#簡介:

操作相關bitcoin的API提供者

[https://blockexplorer.com/api-ref](https://blockexplorer.com/api-ref)

[https://github.com/blockchain/service-my-wallet-v3](https://github.com/blockchain/service-my-wallet-v3)  \(此為跑在自己local端的server\)

[https://www.block.io/docs](https://www.block.io/docs)

[https://blockchain.info/api/blockchain\_api](https://blockchain.info/api/blockchain_api)

[https://chainquery.com/bitcoin-api/getinfo](https://chainquery.com/bitcoin-api/getinfo)

[https://github.com/bitcoinj/bitcoinj](https://github.com/bitcoinj/bitcoinj) \(Java寫的Bitcoin核心   也可使用Nashore來執行javascript\)

[https://www.smartbit.com.au/api](https://www.smartbit.com.au/api)

\#直接與本地的Bitcoind 啟動的JSON-RPC溝通

```
https://en.bitcoin.it/wiki/API_reference_(JSON-RPC)
```

# 使用BitcoinJS

範例:

[https://github.com/bitcoinjs/bitcoinjs-lib/blob/9bae30d1121a9d382f2f461fad0194a0e97dfd1e/test/integration/addresses.js](https://github.com/bitcoinjs/bitcoinjs-lib/tree/9bae30d1121a9d382f2f461fad0194a0e97dfd1e/test/integration)

```
npm install bitcoinjs-lib
```

產生隨機地址

```
var bitcoin = require('bitcoinjs-lib');
var keyPair = bitcoin.ECPair.makeRandom()

// Print your private key (in WIF format)
console.log(keyPair.toWIF())
// => Kxr9tQED9H44gCmp6HAdmemAzU3n84H3dGkuWTKvE23JgHMW8gct

// Print your address
console.log(keyPair.getAddress())
// => 14bZ7YWde4KdRb5YN7GYkToz3EHVCvRxkF
```

產生一組萊特幣的address跟WIF

之後可到此確認[https://live.blockcypher.com/ltc/](https://live.blockcypher.com/ltc/)

```js
var bitcoin = require('bitcoinjs-lib');
var litecoin = bitcoin.networks.litecoin
function rng () { return Buffer.from('random_bufferrandom_buffer______') }
// buffer 長度須為32
var keyPair = bitcoin.ECPair.makeRandom({ network: litecoin, rng: rng })
var wif = keyPair.toWIF()
var address = keyPair.getAddress()
console.log(address)
```

把WIF格式的私鑰轉換為地址

```
    var bitcoin = require('bitcoinjs-lib');
    var keyPair = bitcoin.ECPair.fromWIF('Kxr9tQED9H44gCmp6HAdmemAzU3n84H3dGkuWTKvE23JgHMW8gct');
    var address = keyPair.getAddress();
    console.log(address);
```

產生多重簽章的地址2-of-3 multisig P2SH address

[https://en.bitcoin.it/wiki/Multisignature](https://en.bitcoin.it/wiki/Multisignature)

```js
var bitcoin = require('bitcoinjs-lib');
var pubKeys = [
    '026477115981fe981a6918a6297d9803c4dc04f328f22041bedff886bbc2962e01',
    '02c96db2302d19b43d4c69368babace7854cc84eb9e061cde51cfa77ca4a22b8b9',
    '03c6103b3b83e4a24a0e33a4df246ef11772f9992663db0c35759a5e2ebf68d8e9'
  ].map(function (hex) { return Buffer.from(hex, 'hex') })

  var redeemScript = bitcoin.script.multisig.output.encode(2, pubKeys) // 2 of 3
  var scriptPubKey = bitcoin.script.scriptHash.output.encode(bitcoin.crypto.hash160(redeemScript))
  var address = bitcoin.address.fromOutputScript(scriptPubKey)
  console.log(address)
```

產生交易

```js
var bitcoin = require("bitcoinjs-lib");
var key = bitcoin.ECPair.fromWIF("L1Kzcyy88LyckShYdvoLFg1FYpB5ce1JmTYtieHrhkN65GhVoq73");
var tx = new bitcoin.TransactionBuilder();
tx.addInput("d18e7106e5492baf8f3929d2d573d27d89277f3825d3836aa86ea1d843b5158b", 1);
tx.addOutput("12idKQBikRgRuZEbtxXQ4WFYB7Wa3hZzhT", 149000);
tx.sign(0, key);
console.log(tx.build().toHex());
```

產生較複雜的交易，並廣播到Bitcoin Test Network

```
git clone https://github.com/bitcoinjs/bitcoinjs-lib.git
cd bitcoinjs-lib
npm install
cd /test/integration
```

```
點選transaction.js 然後把code整個刪掉改為如下
```

```js
/* global describe, it */

var assert = require('assert')
var bitcoin = require('../../')
var dhttp = require('dhttp/200')
var testnet = bitcoin.networks.testnet
var testnetUtils = require('./_testnet')


function rng () {
  return Buffer.from('YT8dAtK4d16A3P1z+TpwB2jJ4aFH3g9M1EioIBkLEV4=', 'base64')
}

var alice1 = bitcoin.ECPair.makeRandom({ network: testnet })
var alice2 = bitcoin.ECPair.makeRandom({ network: testnet })
var aliceChange = bitcoin.ECPair.makeRandom({ rng: rng, network: testnet })

// "simulate" on testnet that Alice has 2 unspent outputs
testnetUtils.faucetMany([
  {
    address: alice1.getAddress(),
    value: 5e4
  },
  {
    address: alice2.getAddress(),
    value: 7e4
  }
], function (err, unspents) {

  var tx = new bitcoin.TransactionBuilder(testnet)
  tx.addInput(unspents[0].txId, unspents[0].vout) // alice1 unspent
  tx.addInput(unspents[1].txId, unspents[1].vout) // alice2 unspent
  tx.addOutput('mwCwTceJvYV27KXBc3NJZys6CjsgsoeHmf', 8e4) // the actual "spend"
  tx.addOutput(aliceChange.getAddress(), 1e4) // Alice's change
  // (in)(4e4 + 2e4) - (out)(1e4 + 3e4) = (fee)2e4 = 20000, this is the miner fee

  // Alice signs each input with the respective private keys
  tx.sign(0, alice1)
  tx.sign(1, alice2)

  // build and broadcast to the Bitcoin Testnet network
  dhttp({
    method: 'POST',
    url: 'https://api.ei8ht.com.au:9443/3/pushtx',
//          url: 'http://tbtc.blockr.io/api/v1/tx/push',
    body: tx.build().toHex()
  },() => {})
  // to build and broadcast to the actual Bitcoin network, see https://github.com/bitcoinjs/bitcoinjs-lib/issues/839
})
```

之後會看到如下輸出

```
funding n2n3vHe6BHUwKybSsSSUXK1CtFTafzmR62 w/ 50000
funding mvvrViCXRZD1czZduc4xCixmfG7DpZ7Lkb w/ 70000
```

進入到此網站[https://live.blockcypher.com/btc-testnet](https://live.blockcypher.com/btc-testnet)

然後在右上角輸入地址 即可查看剛才的交易紀錄\(需要稍等一段時間\)

> 一篇不錯的教學:  
> [https://medium.com/@orweinberger/how-to-create-a-raw-transaction-using-bitcoinjs-lib-1347a502a3a\#.gbnwu2863](https://medium.com/@orweinberger/how-to-create-a-raw-transaction-using-bitcoinjs-lib-1347a502a3a#.gbnwu2863)

### \#查看上次交易的Txid:

[https://blockchain.info/](https://blockchain.info/)

到上面網站的輸入框輸入你的比特幣地址即可看到

### \#把產生的交易hex廣播到bitcoin network

先到

[https://live.blockcypher.com/btc/pushtx/](https://live.blockcypher.com/btc/pushtx/)

\(另外一個廣播交易的blockchain.info其廣播後的訊息較不明確，只會產生例如code=-25,-26等訊息，[blockcypher](https://live.blockcypher.com/btc/pushtx/)會明確顯示原因\)

1.貼上如下  
\(此為確實發生的以前交易，但unspend已經被花掉了\)

```
0100000001313eb630b128102b60241ca895f1d0ffca2170d5a0990e094f2182c102ab94aa000000006b483045022100aefbcf847900b01dd3e3debe054d3b6d03d715d50aea8525f5ea3396f168a1fb022013d181d05b15b90111808b22ef4f9ebe701caf2ab48db269691fdf4e9048f4f60121029f50f51d63b345039a290c94bffd3180c99ed659ff6ea6b1242bca47eb93b59fffffffff01983a0000000000001976a914ad618cf4333b3b248f9744e8e81db2964d0ae39788ac00000000
```

接著

2.試看看如下

\(此為確實發生的以前交易，已經發生過此交易，但unspend尚存在\)

```
01000000018b15b543d8a16ea86a83d325387f27897dd273d5d229398faf2b49e506718ed1010000006b483045022100936c08bb158f0769c5af3c15d97be2695fce235ba0377a56476adc91072c871d02206ffb9a57cdbc5462a7eaaeb9bb734214db2c4038e01f3178ae48fea0375a49100121025f1290847ee12913ea1e41b2a36e346001321bac436a3e47c3e7cd133b379b3bffffffff0108460200000000001976a91412d77f0ecfef452efa491af02c995441d25b4fdb88ac00000000
```

3.然後試著把一些字隨便改掉上按送出看看

### \#加密方法:

```
Mnemonic code for generating deterministic keys (BIP 39), credits to Thasshiznets

Hierarchical Deterministic Wallets (BIP 32)

Payment Protocol (BIP 70)

Payment URLs (BIP 21,BIP 72)

Two-Factor keys (BIP 38)
```



