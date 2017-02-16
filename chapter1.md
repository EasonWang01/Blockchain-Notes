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

比特幣的：
https://blockchain.info/block/000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f


其擁有以下結構
```
4字節	版本	版本號，用於跟踪軟件/協議的更新
32字節   父區塊哈希值	引用區塊鏈中父區塊的哈希值
32字節   Merkle根	該區塊中交易的merkle樹根的哈希值
4字節	時間戳	該區塊產生的近似時間（精確到秒的Unix時間戳）
4字節	難度目標	該區塊工作量證明算法的難度目標
4字節	Nonce	用於工作量證明算法的計數器
```