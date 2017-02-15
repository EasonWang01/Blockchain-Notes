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

