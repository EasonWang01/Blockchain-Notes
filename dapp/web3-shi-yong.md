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

呼叫需要交易的 function \(runtime\)

```javascript

```

