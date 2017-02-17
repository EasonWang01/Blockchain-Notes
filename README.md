# #簡介:

操作相關bitcoin的API提供者

https://blockexplorer.com/api-ref   

https://github.com/blockchain/service-my-wallet-v3  (此為跑在自己local端的server)




#使用BitcoinJS

```
npm install bitcoinjs-lib
```


產生隨機地址
```

    function rng () { return new Buffer('zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz') }

    // generate random keyPair
    var keyPair = bitcoin.ECPair.makeRandom({ rng: rng })
    var address = keyPair.getAddress()

```


從SHA256 hash產生地址
```
    var hash = bitcoin.crypto.sha256('correct horse battery staple')
    var d = bigi.fromBuffer(hash)

    var keyPair = new bitcoin.ECPair(d)
    var address = keyPair.getAddress()
```

產生一組萊特比的address跟WIF
```
    function rng () { return new Buffer('zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz') }

    var litecoin = bitcoin.networks.litecoin

    var keyPair = bitcoin.ECPair.makeRandom({ network: litecoin, rng: rng })
    var wif = keyPair.toWIF()
    var address = keyPair.getAddress()
```


把WIF格式的私鑰轉換為地址
```
    var keyPair = bitcoin.ECPair.fromWIF('Kxr9tQED9H44gCmp6HAdmemAzU3n84H3dGkuWTKvE23JgHMW8gct')
    var address = keyPair.getAddress()

```

產生交易
```
    var keyPair = bitcoin.ECPair.fromWIF('L1uyy5qTuGrVXrmrsvHWHgVzW9kKdrp27wBC7Vs6nZDTF2BRUVwy')
    var tx = new bitcoin.TransactionBuilder()

    tx.addInput('aa94ab02c182214f090e99a0d57021caffd0f195a81c24602b1028b130b63e31', 0)
    tx.addOutput('1Gokm82v6DmtwKEB8AiVhm82hyFSsEvBDK', 15000)
    tx.sign(0, keyPair)

```

產生較複雜的交易，並廣播到bitcoin network
```
    var network = bitcoin.networks.testnet
    var alice = bitcoin.ECPair.makeRandom({ network: network })
    var bob = bitcoin.ECPair.makeRandom({ network: network })
    var alicesAddress = alice.getAddress()
    var bobsAddress = bob.getAddress()

    blockchain.t.faucetMany([
      {
        address: alicesAddress,
        value: 4e4
      },
      {
        address: bobsAddress,
        value: 2e4
      }
    ], function (err, unspents) {
      if (err) return done(err)

      var tx = new bitcoin.TransactionBuilder(network)
      tx.addInput(unspents[0].txId, unspents[0].vout)
      tx.addInput(unspents[1].txId, unspents[1].vout)
      tx.addOutput(blockchain.t.RETURN, 3e4)
      tx.addOutput('mvGVHWi6gbkBZZPaqBVRcxvKVPYd9r3fp7', 1e4)
      tx.sign(0, alice)
      tx.sign(1, bob)

      blockchain.t.transactions.propagate(tx.build().toHex(), done)

```

一篇不錯的教學:
https://medium.com/@orweinberger/how-to-create-a-raw-transaction-using-bitcoinjs-lib-1347a502a3a#.gbnwu2863

### #查看上次交易的Txid:
https://blockchain.info/

到上面網站的輸入框輸入你的比特幣地址即可看到


### #把產生的交易hex廣播到bitcoin network

先到

https://blockchain.info/pushtx

貼上如下
(此為確實發生的以前交易)
```
0100000001313eb630b128102b60241ca895f1d0ffca2170d5a0990e094f2182c102ab94aa000000006b483045022100aefbcf847900b01dd3e3debe054d3b6d03d715d50aea8525f5ea3396f168a1fb022013d181d05b15b90111808b22ef4f9ebe701caf2ab48db269691fdf4e9048f4f60121029f50f51d63b345039a290c94bffd3180c99ed659ff6ea6b1242bca47eb93b59fffffffff01983a0000000000001976a914ad618cf4333b3b248f9744e8e81db2964d0ae39788ac00000000
```

接著

試著把一些字改掉上按送出看看



### #加密方法:

```
Mnemonic code for generating deterministic keys (BIP 39), credits to Thasshiznets

Hierarchical Deterministic Wallets (BIP 32)

Payment Protocol (BIP 70)

Payment URLs (BIP 21,BIP 72)

Two-Factor keys (BIP 38)
```


