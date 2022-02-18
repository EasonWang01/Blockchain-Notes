# web3 使用

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

## 監聽合約 Event&#x20;

> 記得 infura 的測試網路 url 要填對，例如 rinkeby 不要寫成 ropsten

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

## 注意事項

使用 CRA 5 版本以上引入 web3 會出現 error

[https://stackoverflow.com/questions/70472965/web3-issue-react-application-not-compiling/70512623#70512623](https://stackoverflow.com/questions/70472965/web3-issue-react-application-not-compiling/70512623#70512623)

解決方法:

```
npm uninstall react-scripts
npm i react-scripts@4.0.3
```

