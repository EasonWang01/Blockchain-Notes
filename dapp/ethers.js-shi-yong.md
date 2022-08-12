# Ethers.js 使用

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

### 呼叫合約：

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
