# 在網頁上使用 web3 並操作區塊鏈

## 前言

Web3 與 Web2 的不同之處在於 "去中心化"，由分散且不同人運營的 Server 來進行交易的驗證與維護帳本。而在乙太坊上因為借力於智能合約，可以讓我們執行更多樣化的邏輯與交易，而為了讓用戶可以輕易地與這些智能合約互動，我們需要一個前端頁面，這時就是 Web3.js 發揮功能的時候。

很多人會問： Web3 是什麼？不也是跟之前一樣用網頁來瀏覽網站嗎，筆者本人當時接觸到 Web3 這個詞是從一個 js 框架叫做 Web3.js 開始，Web3 可以說是一個概念也可以說是一個架構，雖然去中心化以及區塊鏈這些概念在多年前已經有類似的實現，但使用不同的名詞，Web3 改變的底層協議或許不大，但也許是人們對於另一個階段的集體共識的體現，以自己所理解的來認知即可。

我們這邊使用 React.js 前端框架來建構 UI 並使用 web3.js (也可選擇使用 ether.js) 來與剛才建立的 RPC server 溝通，達到一個網頁控制區塊鏈的 DApp。

用戶在與 Dapp 網頁互動的時候會於瀏覽器安裝 Metamask 擴充工具，其為一個區塊鏈錢包，用來存放代幣，並且於執行網頁互動時支付交易手續費。

## 2021 更新： Metamask connect to web3

> web3 v1.3.4

```javascript
const Web3 = require("web3");
const ethEnabled = () => {
  if (window.ethereum) {
    window.web3 = new Web3(window.ethereum);
    window.ethereum.enable();
    return true;
  }
  return false;
}
ethEnabled();
```

### 其他連線 web3 方式

Wallet Connect: [https://github.com/WalletConnect/web-examples](https://github.com/WalletConnect/web-examples)

Ether.js: [https://docs.ethers.io/v4/api-providers.html](https://docs.ethers.io/v4/api-providers.html)

Web3Modal: [https://github.com/Web3Modal/web3modal](https://github.com/Web3Modal/web3modal)

## Dapp 範例

#### 安裝 React

使用 create-react-app 建構前端環境，這邊我們選擇連線到自行架設的乙太坊測試節點 Geth。

```
npm install -g create-react-app
npm install -g yarn

create-react-app my-dapp
cd my-dapp

yarn add web3@0.18.0 
yarn start
```

這裡記得因為我們要用瀏覽器存取RPC server，所以geth啟動時RPC server要加上CORS`--rpccorsdomain="*"`

```
geth  --ipcdisable --rpc --rpcport 8114 --datadir "./privatechain/03" --networkid 123 --rpcapi="db,eth,net,web3,personal" --nodiscover  --port=30319 --rpccorsdomain="*"   console
```

> web3 之API文件
>
> [https://github.com/ethereum/wiki/wiki/JavaScript-API](https://github.com/ethereum/wiki/wiki/JavaScript-API)

把src/App.js改為如下

```javascript
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
var Web3 = require('web3');
var web3 = new Web3();

class App extends Component {

  constructor() {
    super();
    this.state = {
      accounts: ''
    }
  }

  componentWillMount() {
    console.log(web3);
    web3.setProvider(new web3.providers.HttpProvider('http://localhost:8104')); //指定為RPC server的位置
    this.setState({ accounts: web3.eth.accounts });
    console.log(web3.eth.accounts)
  }

  render() {
    return (
      <div className="App">
        <div className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h2>Welcome to My Dapp</h2>
        </div>
        <p className="App-intro">
          {this.state.accounts.map((i, idx) => (
            <p>帳號{idx}: {i} </p>
          ))}
        </p>
      </div>
    );
  }
}

export default App;
```

> web3版本如果大於1.0.0 要改為如下
>
> 主要是大部份web3 function 回傳值都改為要從callback第二個參數取出

```javascript
  web3.eth.getAccounts(function(err, result){
    context.setState({accounts: result});
  });
```

即可看到列出我們的帳號

接著新增一個資料夾`contract`裡面放入`contract01.js`把我們剛才的contract的地址以及內容放入(記得要先去掉at)

```javascript
module.exports = {
  ABI: [{constant: false,
        inputs: [{
            name: "receiver",
            type: "address"
        }, {
            name: "amount",
            type: "uint256"
        }],
        name: "sendCoin",
        outputs: [{
            name: "sufficient",
            type: "bool"
        }],
        payable: false,
        type: "function"
    }, {
        constant: true,
        inputs: [{
            name: "",
            type: "address"
        }],
        name: "coinBalanceOf",
        outputs: [{
            name: "",
            type: "uint256"
        }],
        payable: false,
        type: "function"
    }, {
        inputs: [{
            name: "supply",
            type: "uint256"
        }],
        payable: false,
        type: "constructor"
    }, {
        anonymous: false,
        inputs: [{
            indexed: false,
            name: "sender",
            type: "address"
        }, {
            indexed: false,
            name: "receiver",
            type: "address"
        }, {
            indexed: false,
            name: "amount",
            type: "uint256"
        }],
        name: "CoinTransfer",
        type: "event"
    }],

  address: "0x14067c5707025c4dabba49949c0c166070b4f5c9"
}
```

上面的Address 可在geth console輸入以下查看

```
token.address
```

之後App.js改為如下

```javascript
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';

import contract01 from './contract/contract01.js' //引入contract
var Web3 = require('web3');
var web3 = new Web3();

class App extends Component {

  constructor() {

    super();
    this.state = {
      accounts : ''
    }
  }

  componentWillMount() {
    console.log(web3);
    window.tokenContract = web3.eth.contract(contract01.ABI).at(contract01.address);
    window.web3 = web3;
    web3.setProvider(new web3.providers.HttpProvider('http://localhost:8104')); //指定為RPC server的位置
    this.setState({ accounts: web3.eth.accounts });

  }

  render() {
    console.log(window.tokenContract.coinBalanceOf("0x66a2e289b35147188876c2007f9a810dd20e480d"))
    return (
      <div className="App">
        <div className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h2>Welcome to My Dapp</h2>
        </div>
        <p className="App-intro">
          {this.state.accounts.map((i,idx) => (
              <p key={idx}>帳號{idx}:  {i}  ，餘額: {window.tokenContract.coinBalanceOf(i).c[0]}</p>
          ))}
        </p>
      </div>
    );
  }
}

export default App;
```

即可看到

![](<../../.gitbook/assets/螢幕快照 2017-02-16 上午12.22.48.png>)

記得把程式碼 改為剛才有餘額的地址

加入簡單轉帳功能

```javascript
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';

import contract01 from './contract/contract01.js' //引入contract
var Web3 = require('web3');
var web3 = new Web3();

class App extends Component {

  constructor() {

    super();
    this.state = {
      accounts : ''
    }
  }

  componentWillMount() {
    window.tokenContract = web3.eth.contract(contract01.ABI).at(contract01.address); //初始化合約
    window.web3 = web3;
    web3.setProvider(new web3.providers.HttpProvider('http://localhost:8104')); //指定為RPC server的位置
    this.setState({ accounts: web3.eth.accounts });

    web3.personal.unlockAccount(web3.eth.accounts[0], "12345") //解鎖帳號，這裡記得要填入密碼，每次重啟geth都須重新解鎖

  }
  send() {
    window.tokenContract.sendCoin.sendTransaction(web3.eth.accounts[1], this.state.coin, {from: web3.eth.accounts[0]})
  }
  render() {
    console.log(window.tokenContract);
   
    return (
      <div className="App">
        <div className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h2>Welcome to My Dapp</h2>
          <button onClick={() => this.send()}>轉帳</button>
          <input onChange={(e) => this.setState({coin: e.target.value})} />
        </div>
        <p className="App-intro">
          {this.state.accounts.map((i,idx) => (
              <p key={idx}>帳號{idx}:  {i}  ，餘額: {window.tokenContract.coinBalanceOf(i).c[0]}</p>
          ))}
        </p>
      </div>
    );
  }
}

export default App;
```

![](<../../.gitbook/assets/螢幕快照 2017-03-25 下午8.42.42.png>)
