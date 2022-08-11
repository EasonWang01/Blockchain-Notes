# web3.js 使用

v1.0 有 API更改，以下以 v1.0 為主

```javascript
if (window.ethereum) {
  window.web3 = new Web3(window.ethereum);
  window.ethereum.enable();
}
try {
  var web3 = window.web3;
  var web3 = new Web3(web3.currentProvider);
} catch (err) {
  alert("Please install Metamask first");
  // window.location =
  //   "https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn";
}

const ContractERC721 = new web3.eth.Contract(ERC721.ABI, ERC721.address);
window.ContractERC721 = ContractERC721;
```

執行不需要交易的 function

```javascript
const totalSupply = await ContractERC721.methods.totalSupply().call();
console.log(totalSupply.toNumber())
```

## Call static

https://ethereum.stackexchange.com/a/109992



## 監聽合約 Event&#x20;

> 記得 infura 的測試網路 url 要填對，例如 Rinkeby 不要寫成 ropsten

```javascript
const Web3 = require("web3");

const provider = new Web3.providers.WebsocketProvider(
  "wss://rinkeby.infura.io/ws/v3/<API KEY>"
  );
const CONTRACT_ADDRESS = "0x00129c695b5c3d95b1e9511c4d454dc6ab276936";
const ABI = require("./abi/t.js");
provider.on("connect", () => {
  console.log("Websocket connected.");
});

provider.on("close", (event) => {
  console.log(event);
  console.log("Websocket closed.");
});

provider.on("error", (error) => {
  console.error(error);
});

const web3 = new Web3(provider);
const testContract = new web3.eth.Contract(ABI, CONTRACT_ADDRESS);
testContract.events
  .TDeposit()
  .on("connected", (id) => {
    console.log(`TDeposit subscription connected (${id})`);
  })
  .on("data", (event) => {
    console.log('ondata', event);
  })
  .on("error", (error) => {
    console.log(error);
  });
```

## 後端使用ECDSA 簽名與驗證

使用 createIdentity 產生私鑰與公鑰，並且公鑰產生地址，之後用私鑰簽名(sign)然後用 recovery 還原出簽名的地址。

```javascript
const EthCrypto = require("eth-crypto");

const identity = EthCrypto.createIdentity();

console.log(identity);

const message = "foobar";
const messageHash = EthCrypto.hash.keccak256(message);
const signature = EthCrypto.sign(
  identity.privateKey, // privateKey
  messageHash // hash of message
);

const signer = EthCrypto.recover(
  signature,
  EthCrypto.hash.keccak256("foobar") // signed message hash
);

const address = EthCrypto.publicKey.toAddress(identity.publicKey);

console.log("address", address);

console.log("signer", signer);
```

## 前端使用簽名與驗證

只有前端能用 eth.personal

```javascript
  var signature = await web3.eth.personal.sign("Hello", accounts[0])
  console.log(signature)

  const signer = await web3.eth.personal.ecRecover("Hello", signature)
  console.log(signer) // account[0]
```

使用後端驗證前端的簽名，使用 eth-sig-util 模組

```javascript
const ethSigUtil = require("eth-sig-util");
function checkSignature(nonce, signature) {
    const msgParams = {
        data: nonce,
        sig: signature
    };
    return ethSigUtil.recoverPersonalSignature(msgParams);
}
console.log(checkSignature(
  "Hello", //貼上 metamask 簽名的內容，不用 hash
  <貼上 metamask sign 出的簽名>,
))
```

或是使用 eth-crypto 模組

```javascript
const EthCrypto = require("eth-crypto");
const Web3 = require("web3");
const provider = new Web3.providers.WebsocketProvider(
  "wss://rinkeby.infura.io/ws/v3/..."
);
const web3 = new Web3(provider);

const msg = "Hello";
 const messageHash1 =  web3.utils.soliditySha3(
    { type: "string", value: `\x19Ethereum Signed Message:\n${msg.length}`}, // 記得 :\n 後面數字要改成你的訊息長度
    { type: "string", value: msg }
  );
const signer1 = EthCrypto.recover(
  "<貼上 metamask sign 出的簽名>",
  messageHash1
);
console.log('signer1', signer1)
```

> 有時看到前端簽名的內容是比較格式化的，可以參考：sign-typed-data-v4
>
> [https://docs.metamask.io/guide/signing-data.html#sign-typed-data-v4](https://docs.metamask.io/guide/signing-data.html#sign-typed-data-v4)

## 重新整理與初始化頁面時的 web3 地址顯示與簽名

```javascript
  useEffect(() => {
    if (localStorage.getItem(accountLocalStorageKey)) {
      initWeb3();
    }
  }, []);

  const initWeb3 = async () => {
    if (window.ethereum) {
      window.web3 = new Web3(window.ethereum);
      window.ethereum.enable();
    }
    try {
      var web3 = window.web3;
      var web3 = new Web3(web3.currentProvider);
    } catch (err) {
      alert("Please install Metamask first");
    }

    const checkActive = async () => {
      const accounts = await web3.eth.getAccounts();
      if (accounts[0] && !localStorage.getItem(accountLocalStorageKey)) {
        // Init
        localStorage.setItem(accountLocalStorageKey, accounts[0]);
        setCurrentAccount(accounts[0]);
        var signature = await web3.eth.personal.sign(
          signMsg,
          accounts[0]
        );
        localStorage.setItem(accountSigLocalStorageKey, signature);
        console.log(signature);
      }
      setCurrentAccount(accounts[0]);
      if (!accounts[0]) {
        localStorage.removeItem(accountLocalStorageKey);
        localStorage.removeItem(accountSigLocalStorageKey);
      }
    };
    checkActive();
    setInterval(checkActive, 1500);
  };
```

## 建立 raw transaction

建立交易但不廣播：

web3.js 版本

```
myContract.methods.myMethod(123).encodeABI();
```

&#x20;ether.js 版本

```javascript
myContract.populateTransaction.myMethod(123);
```

## 後端使用私鑰發送交易

```javascript
const EthereumTx = require("ethereumjs-tx").Transaction;
const Web3 = require("web3");
const web3 = new Web3(
  new Web3.providers.HttpProvider(
    "https://ropsten.infura.io/v3/9aa3d95b3bc440fa88ea12eaa4456161"
  )
);

function signTx(tra) {
  return new Promise(async (resolve, reject) => {
    const prvKey = Buffer.from(process.env.prvkey, "hex");
    const accountNonce = await web3.eth.getTransactionCount(process.env.ethereumAccount)
    var tx = new EthereumTx(
      {
        nonce: accountNonce,
        gasPrice: 100,
        gasLimit: 100000,
        value: 100000,
      },
      { chain: "ropsten" }
    );
    tx.sign(prvKey);
    var stx = tx.serialize();
    web3.eth.sendSignedTransaction("0x" + stx.toString("hex"), (err, hash) => {
      if (err) {
        console.log(err);
        reject(err);
        return;
      }
      resolve(hash);
      console.log("tx creation: " + hash);
    });
  });
}
```

轉帳交易的話 tx 內容改如下即可

```javascript
{
      gasPrice: 12, // (gwei)
      gasLimit: 21000, 
      to: "to address",
      value: Number(web3.utils.toWei("0.0001", "ether")),
}
```

#### 如果出現 Invalid sender 的話記得檢查下 web3 rpc url 跟 chainid 有沒有對應：

```javascript
const web3 = new Web3(
  new Web3.providers.HttpProvider("https://bsc-dataseed1.binance.org/")
);
const common = Common.custom({ chainId: 56 });
```

## 單位換算

不同 ERC-20 合約 token 有不同的 decimals，例如 USDC 為 6，大部分為  18 等等。

#### 讀取合約餘額然後到網頁顯示

今天讀取到合約的 balanceOf 後會需要除以 decimals，例如 `balance / 1e18`

#### 換算回原本的精度然後寫回合約

如果今天合約是要 deposit ，並且用原本 token 的精度的話，已經除以 1e18 的數字可以如下返回原本的精度，然後寫回合約，記得要用 bigNumber，不然 js 會有問題。

```javascript
  const toBN18 = (value) => {
    const web3Utils = window.web3.utils;
    return new web3Utils.BN(
      web3Utils.toWei(String(value)) // toWei 剛好為 * 1e18
    );
  }
```

&#x20;![](<../.gitbook/assets/截圖 2022-04-01 下午12.00.21.png>)

## Approve max

```javascript
        const result1 = await tokenContract.methods
          .approve(
            VaultRelayerAddress,
            String(new web3Utils.BN(String('100000000000' + 1 * `1e${token.decimal}`)))
          )
          .send({
            from: accounts[0],
          });
```

## 轉換 blockNumber 為 timestamp

> 轉為 UTC +8

```javascript
  const blockInfo = await web3.eth.getBlock(blockNumber);
  const timestamp = (blockInfo.timestamp) * 1000 + (60 * 60 * 8 * 1000)
  console.log(timestamp);
```

## 注意事項

使用 CRA 5 版本以上引入 web3 會出現 error

[https://stackoverflow.com/questions/70472965/web3-issue-react-application-not-compiling/70512623#70512623](https://stackoverflow.com/questions/70472965/web3-issue-react-application-not-compiling/70512623#70512623)

解決方法:

```
npm uninstall react-scripts
npm i react-scripts@4.0.3
```

