# #產生公鑰和私鑰與比特幣地址

```
十六進制hex每個字為4 bits也就是0.5bytes
```

以下使用node.js實作
先安裝`npm install bs58`


```
var crypto = require('crypto');
var ecdh = crypto.createECDH('secp256k1');


var hash2 = crypto.randomBytes(32)
console.log('--------')
console.log('私鑰')
console.log(hash2); //私鑰，64位十六進制數   //使用hash2.toString('hex')即可看到16進位字串
console.log('--------')



var publickey = ecdh.setPrivateKey(hash2,'hex').getPublicKey('hex')
console.log('公鑰')
console.log(publickey); //公鑰(通過橢圓曲線算法可以從私鑰計算得到公鑰)
console.log('--------')

//把公鑰以sha256加密後再用ripemd160加密，取得publickeyHash
var hash = crypto.createHash('sha256').update(publickey).digest();
hash = crypto.createHash('ripemd160').update(hash).digest();
console.log('publickeyHash')
console.log(hash);
console.log('--------')


//在publickeyHash前面加上一个00前缀
var version = new Buffer('00', 'hex');
var checksum = Buffer.concat([version, hash]);
//兩次256雙重加密
checksum = crypto.createHash('sha256').update(checksum).digest();
checksum = crypto.createHash('sha256').update(checksum).digest();

//取前4位得到效驗碼
checksum = checksum.slice(0, 4);
console.log('checksum')
console.log(checksum);
console.log('--------')

//把publickeyHash前面一樣加上00而後面加上剛才算出的checksum
var address = Buffer.concat([version, hash, checksum]);
console.log('編碼前地址')
console.log(address);
console.log('--------')


var bs58 = require('bs58');
address = bs58.encode(address);
console.log('編碼後的比特幣地址')
console.log(address);
console.log('--------')
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

bitcoin genesis 創建的原始碼
https://github.com/bitcoin/bitcoin/blob/3955c3940eff83518c186facfec6f50545b5aab5/src/chainparams.cpp#L123


# #Merkel tree

區塊鏈中的每個區塊都包含了產生於該區塊的所有交易，且以Merkle樹表示


![](/assets/螢幕快照 2017-02-16 下午4.00.56.png)

他是把每一筆資料的txid用兩次sha256做加密

```
HA =  SHA256(SHA256(交易A))
```

```
HB =  SHA256(SHA256(交易B))
```
然後再把兩個字串連結再一起，之後再繼續做一樣的加密，直到出現Merkel根為止


ex:

```
var crypto = require('crypto');


var tx1 = '51b78168d94ec307e2855697209275d477e05d8647caf29cb9e38fb6a4661145';
var tx2 = 'dasd94ec307e2855697209275d477e05d8647caf29cb9e38fb6a4661145ddddd';

function crypto256(input) {
  var final = crypto.createHmac('sha256', input)
                .update('test')
                .digest('hex');
  return final
} 

var hash1 = crypto256(crypto256(tx1));

var hash2 = crypto256(crypto256(tx2));

console.log(hash1);
console.log(hash2);

var root = hash1 + hash2;

console.log('ROOT為:' + root);
```

# #挖礦


在挖礦過程中成功“挖出”新區塊的礦工可以得到該區塊中包含的所有交易手續費。目前，這筆費用占礦工收入的0.5%或更少，大部分收益仍來自挖礦所得的比特幣獎勵

可以先參考此網站
http://www.yogh.io/#mine:last

他會用區塊頭
```
Version: 536870912
Prev block: 0000000000000000008D8AF3B55F92BFFEBF286D9C87C54F80C780224F8DD06C
Merkle root: AA5FB4AFB0154D2BDD3315E074F219351FDF13908F1C515E07BE12124A3D3760
Timestamp: February 16, 2017, 17:35:35 +0800
Bits: 18029AB9
Nonce: 一個隨機數

```
來做兩次sha256加密，只要比一個target數小，及為挖到新的區塊

```
var crypto = require('crypto');


function crypto256(input) {
  var final = crypto.createHmac('sha256', input)
                    .update('test')
                    .digest('hex');
  return final
} 

var hash1 = (header) => crypto256(crypto256(header));

var nonce = 0;
while(1) {
  nonce += 1;
  
  var header = {
      nonce: nonce,
      previousHash: "dd0e2b79d79be0dfca96b4ad9ac85600097506f06f52bb74f769e02fcc66dec6",
      merkleRoot: "c91c008c26e50763e9f548bb8b2fc323735f73577effbc55502c51eb4cc7cf2e",
      TimeStamp: new Date()
  };
  var cal = hash1(JSON.stringify(header));
  console.log(cal);
  if (parseInt(cal, 16) < parseInt("0000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF", 16)) {
    console.log("success: " + cal);
    console.log("counts for:" + nonce + ' tiems')
    break;
  }

}


```

第一筆礦工挖礦的收入交易通常被稱為coinbase，它沒有輸入，所以TxIn的Hash總是被標記為00000000...0000

![](/assets/螢幕快照 2017-02-17 上午12.00.21.png)


你可能會想說為什麼不直接給小於某一個數字就好了為何還要慢慢算呢，因為算出來後要給別人驗證你已經算出來的話你必須給別人本文，也就是加密前的東西，讓別人用你的本文加密，來確認真的可以用這個本文算出特定hash


# #廣播與驗證交易

https://en.bitcoin.it/wiki/Protocol_rules#.22tx.22_messages

發出交易後發出者會帶上用私鑰與相關交易訊息hash過的值，而發出的script包含公鑰，之後其他節點要驗證交易時就用這個公鑰看能不能解開即可

意思為用發出者的公鑰解開他用私鑰加密後的東西即為交易本文


# #難度difficulty的更改
難度在每2016個block會更改一次
