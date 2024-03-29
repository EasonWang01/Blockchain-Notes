# web3.js 使用

> 目前主流的 ETH 測試網路為 Sepolia

v1.0 有 API更改，以下以 v1.0 為主

## 初始化 Web3.js

> v4 版本需要用以下方式引入

```javascript
const { Web3 } = require("web3");
const web3 = new Web3();
```

## 初始化合約

```javascript
if (window.ethereum) {
  window.web3 = new Web3(window.ethereum); // 參數內填入 provider
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

## 執行不需要交易的 function

```javascript
const totalSupply = await ContractERC721.methods.totalSupply().call();
console.log(totalSupply.toNumber())
```

## Big Number

```
web3Utils.BN
```

> 建議改用 npm: bignumber.js，多了很多錯誤處理。

## 監聽帳戶轉帳交易

> 這邊使用 Alchemy API
>
> 免費幣水龍頭：[https://sepoliafaucet.com/](https://sepoliafaucet.com/)
>
> 新增 Alchemy APP: [https://dashboard.alchemy.com/apps](https://dashboard.alchemy.com/apps)

#### Pending Transaction

[https://docs.alchemy.com/reference/alchemy-pendingtransactions](https://docs.alchemy.com/reference/alchemy-pendingtransactions)

```javascript
const { Network, Alchemy } = require("alchemy-sdk");

// Optional Config object, but defaults to demo api-key and eth-mainnet.
const settings = {
  apiKey: "", // Replace with your Alchemy API Key.
  network: Network.ETH_SEPOLIA, // Replace with your network.
};

const alchemy = new Alchemy(settings);

/// Subscription for Alchemy's pendingTransactions API
alchemy.ws.on(
  {
    method: "alchemy_pendingTransactions",
    toAddress: "0x220356c76414A3a12843560352ADb69443956196", // Replace with the address you're monitoring.
    fromAddress: "sender_address", // Optionally, replace with the sender's address.
  },
  (tx) => console.log(tx)
);
```

#### Mined Transaction (confirmed transaction)

```javascript
const { Network, Alchemy } = require("alchemy-sdk");

// Optional Config object, but defaults to demo api-key and eth-mainnet.
const settings = {
  apiKey: "", // Replace with your Alchemy API Key.
  network: Network.ETH_SEPOLIA, // Replace with your network.
};

const alchemy = new Alchemy(settings);

/// Subscription for Alchemy's minedTransactions API
alchemy.ws.on(
  {
    method: "alchemy_minedTransactions",
    addresses: [{ to: "0x220356c76414A3a12843560352ADb69443956196" }],
  },
  (tx) => console.log(tx)
);
```

## 獲取帳戶歷史交易

> Alchemy SDK 的 rate limit 用 CUP 計算，免費帳戶的 CUP 為 330 / sec，而 getAssetTransfers 耗費 150 CUP，所以每秒只能發送兩個請求
>
> [https://docs.alchemy.com/reference/pricing-plans](https://docs.alchemy.com/reference/pricing-plans)

```javascript
const fetchTx = async (toAddress) => {
  //The response fetches the transactions the specified addresses.
  try {
    let response = await alchemy.core.getAssetTransfers({
      fromBlock: "0x47c66d",
      toAddress,
      excludeZeroValue: true,
      category: ["external"],
    });

    return response;
  } catch (error) {
    console.log(error);
  }
};
```

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

## 後端使用私鑰發送轉帳交易

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

> 建議可以用 bignumber.js

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

## Approve max allowance

> address spend other address's ERC20 token

```javascript
const ethers = require("ethers");
const MAX = ethers.BigNumber.from('0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff')       
const result = await tokenContract.methods
    .approve(
        VaultRelayerAddress,
        MAX
    )
    .send({
      from: accounts[0],
    });
```

## 從後端私鑰發送合約交易

```javascript
const Web3 = require("web3");
const BigNumber = require("bignumber.js");
const { ABI } = require("../abi/megaswapper");
const web3 = new Web3(
  new Web3.providers.HttpProvider("https://bsc-dataseed1.binance.org/")
);
const account = web3.eth.accounts.privateKeyToAccount(
  "0x" + process.env.prvkey
);
web3.eth.accounts.wallet.add(account);
web3.eth.defaultAccount = account.address;

const decimalStr = (value, decimal) => {
  return new BigNumber(value)
    .multipliedBy(10 ** decimal)
    .toFixed(0, BigNumber.ROUND_DOWN);
};
const megaSwapperAddress = ".....";
function swap(tra) {
  return new Promise(async (resolve, reject) => {
    try {
      const tokenContract = new web3.eth.Contract(ABI, megaSwapperAddress);
      const result = await tokenContract.methods
        .swap(
          tra.fromToken,
          tra.toToken,
          decimalStr(String(tra.amountIn), tra.fromTokenDecimal),
          tra.from,
          tra.to,
          tra.data
        )
        .send({
          gas: 500000,
          from: web3.eth.defaultAccount,
        });
      console.log(result);
      resolve(result);
    } catch (err) {
      console.log(err);
      reject(err);
    }
  });
}
```

## 轉換 blockNumber 為 timestamp

> 轉為 UTC +8

```javascript
  const blockInfo = await web3.eth.getBlock(blockNumber);
  const timestamp = (blockInfo.timestamp) * 1000 + (60 * 60 * 8 * 1000)
  console.log(timestamp);
```

## 獲取合約 event 歷史

```javascript
const getUSDTTransfers = async () => {
  const Web3 = require("web3");
  const erc20ABI = require("./abis/USDT")
  const provider = new Web3.providers.HttpProvider(
    "https://eth-mainnet.alchemyapi.io/v2/<api key>"
  );

  const web3 = new Web3(provider);

  const currentBlock = await web3.eth.getBlockNumber();
  const contract = new web3.eth.Contract(erc20ABI, USDT_ADDRESS);
  contract
    .getPastEvents("Transfer",{
      fromBlock: currentBlock - 1000,
      toBlock: "latest",
      filter: {to: "0x28C6c06298d514Db089934071355E5743bf21d60"},
    })
    .then(async (events) => {
      console.log(events)
    });
};
```

## 注意事項

1.使用 CRA 5 版本以上引入 web3 會出現 error

[https://stackoverflow.com/questions/70472965/web3-issue-react-application-not-compiling/70512623#70512623](https://stackoverflow.com/questions/70472965/web3-issue-react-application-not-compiling/70512623#70512623)

解決方法:

```
npm uninstall react-scripts
npm i react-scripts@4.0.3
```

2.Metamask - "Params specify an EIP-1559 transaction but the current network does not support EIP-1559"

有些網路還沒升級到 EIP1559後版本，所以 metamask 有時呼叫 function 後會出現此錯誤。

解決方法:

> add type: "0x1"

```javascript
const response = await contract.methods.stake().send({
  from: userAddress,
  value: web3.utils.toWei(stakeAmount, "ether").toString(),
  type: "0x1",
});
```

[https://ethereum.stackexchange.com/a/119863](https://ethereum.stackexchange.com/a/119863)
