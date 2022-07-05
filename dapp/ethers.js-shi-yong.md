# Ethers.js 使用

讀取地址餘額：

```javascript
const { ethers } = require("ethers");

let provider = new ethers.providers.InfuraProvider('rinkeby');

 let address = "0x00....";

 provider.getBalance(address).then((balance) => {
     let etherString = ethers.utils.formatEther(balance);
     console.log("Balance: " + etherString);
 });
```

呼叫合約：

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
