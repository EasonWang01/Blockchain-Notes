## #前言
我們這邊使用React.js前端框架來建構UI並使用web3.js來與剛才建立的RPC server溝通，達到一個網頁控制區塊鏈的Dapp


## #安裝React

因為官方有出一個快速環境包，所以我們使用這個不用在自己架設環境

```
npm install -g create-react-app

create-react-app my-Dapp
cd my-Dapp

npm install web3 --save
yarnpkg start

```

這裡記得因為我們要用瀏覽器存取RPC server，所以geth啟動時RPC server要加上CORS`--rpccorsdomain="*"`

```
geth  --ipcdisable --rpc --rpcport 8114 --datadir "./privatechain/03" --networkid 123 --rpcapi="db,eth,net,web3,personal" --nodiscover  --port=30319 --rpccorsdomain="*"   console  
```

把src/App.js改為如下

```
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
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
    web3.setProvider(new web3.providers.HttpProvider('http://localhost:8114')); //指定為RPC server的位置
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
          {this.state.accounts.map((i,idx) => (
              <p>帳號{idx}:  {i} </p>
          ))}
        </p>
      </div>
    );
  }
}

export default App;

```

即可看到列出我們的帳號

接著新增一個資料夾`contract`裡面放入`contract01.js`把我們剛才的contract的地址以及內容放入(記得要先去掉at)

```
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

```
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';

import contract01 from '../contract/contract01.js' //引入contract
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
    web3.setProvider(new web3.providers.HttpProvider('http://localhost:8103')); //指定為RPC server的位置
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

![](/assets/螢幕快照 2017-02-16 上午12.22.48.png)
