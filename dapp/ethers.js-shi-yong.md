# Ethers.js 使用

### 單位  Ether 轉 Wei

> return bigint (e.g. 12000000n)

<pre class="language-typescript"><code class="lang-typescript"><strong>ethers.utils.parseUnits("0.11", "ether")
</strong></code></pre>

### 單位  Wei 轉 Ether

> return string (ether string)

```javascript
ethers.utils.formatEther(balance);
```

### 讀取地址餘額：

```javascript
const { ethers } = require("ethers");

let provider = new ethers.providers.InfuraProvider('rinkeby');

 let address = "0x00....";

 provider.getBalance(address).then((balance) => {
     let etherString = ethers.utils.formatEther(balance);
     console.log("Balance: " + etherString);
 });
```

### 執行合約

```javascript
const { Contract, providers, utils: Utils, Wallet, ethers } = require("ethers");

const {
  abi,
} = require("./artifacts/contracts/<contract>.sol/<contract>.json");

async function createMarket(
  privateKey,
  title
) {
  const provider = new ethers.JsonRpcProvider(url);

  // Create a wallet using the private key
  const wallet = new ethers.Wallet(privateKey, provider);

  // Connect the wallet to the contract
  const contract = new ethers.Contract(contractAddress, abi, wallet);
  return new Promise(async (resolve, reject) => {
    try {
      // Sign and send the transaction using the wallet
      const tx = await wallet.sendTransaction({
        to: contractAddress,
        data: contract.interface.encodeFunctionData("createMarket", [
          title
          ]),
      });

      // Wait for the transaction to be mined
      await tx.wait();
      resolve(tx);
      console.log("Market created successfully!");
    } catch (error) {
      reject();
      console.error("Error creating market:", error);
    }
  });
}
```

### 讀取合約：

```javascript
const { ethers } = require("ethers");
const { abi } = require("./abi/...");

let provider = new ethers.providers.InfuraProvider('rinkeby');

let contractAddress = "0x...";
let contract = new ethers.Contract(contractAddress, abi, provider);

(async () => {
  let currentValue = await contract.symbol();
  console.log(currentValue);
})()
```

### 前端使用 sendTransaction 與 signMessage

```javascript
const provider = new ethers.providers.Web3Provider(web3.currentProvider);
const signer = provider.getSigner();

const tx = await signer.sendTransaction(tx);
const signature = await signer.signMessage("Hello world");
```

### 從後端呼叫 function

> 後端所以需要直接填入私鑰

```javascript
const { ethers } = require("ethers");
const { abi } = require("./abi/...");

let provider = new ethers.providers.InfuraProvider('rinkeby');

let contractAddress = "0x...";

let contract = new ethers.Contract(contractAddress, abi, provider);

 let privateKey = '...';
 let wallet = new ethers.Wallet(privateKey, provider);

 let contractWithSigner = contract.connect(wallet);

(async () => {
  let currentValue = await contractWithSigner.getUserRefundList();
  console.log(currentValue);
})()
```

## Call static

> 通常會用在於獲取執行 function 的回傳值

{% embed url="https://ethereum.stackexchange.com/a/109992" %}

## JSON 內有 Bigint 結構轉換

例如出現：TypeError: Do not know how to serialize a BigInt

可以使用以下方式

```javascript
const toObject = (obj) => {
  return JSON.parse(JSON.stringify(obj, (key, value) =>
      typeof value === 'bigint'
          ? value.toString()
          : value // return everything else unchanged
  ));
}
```
