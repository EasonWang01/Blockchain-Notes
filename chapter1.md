# #產生公鑰和私鑰

http://zhibimo.com/read/wang-miao/mastering-bitcoin/Chapter04.html

以下使用node.js實作
```
var crypto = require('crypto');
var hash2 = crypto.createHmac('sha256', 'hash1')
                   .update('powered by flowchain')
                   .digest('hex');

                   console.log(hash2);  //私鑰
console.log('--------')


var ecdh = crypto.createECDH('secp256k1');
var x = ecdh.setPrivateKey(hash2,'hex');

console.log(ecdh.getPublicKey('hex')); //公鑰(使用橢圓算法)
```

# #genesis block

也稱為創世區塊，為區塊鏈在一開始產生時的區塊

```

```