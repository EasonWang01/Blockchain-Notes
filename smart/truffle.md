# 簡單範例

> 如果有安裝metamask記得要換一個瀏覽器

記得先安裝

```
npm install -g truffle ethereumjs-testrpc
```

我們這邊選擇一個叫做webpack的模板

```
mkdir truffleTest
cd truffleTest
truffle init webpack //使用名稱為webpack的範例模板
```

[https://github.com/trufflesuite/truffle-init-webpack](https://github.com/trufflesuite/truffle-init-webpack)

之後會開始下載 然後進入專案

之後啟動testrpc  `開一個termaial然後輸入testrpc`

之後進入專案路徑的terminal輸入`truffle compile`

然後部署到testrpc跑的測試鏈上`truffle migrate`

```
之後可以試著用頁面轉token
然後更改地40行account = accounts[0]; 的數字查看
```

#### 

#### 106/05/04

[https://github.com/ConsenSys/truffle](https://github.com/ConsenSys/truffle)

```
npm install -g truffle
```

#### 1.

新增一個資料夾cd進去，之後執行

```
truffle init
```

開啟testRPC

```
npm install -g ethereumjs-testrpc
```

之後輸入`testrpc`，他會預設給你九組帳號和對應私鑰

3.在contract下新增一個HelloWorld.sol

```
pragma solidity ^0.4.8;

contract HelloWorld {

    uint public balance;

    function HelloWorld() {
        balance = 2000;
    }

    function deposit(uint _value) {
      // ...
      balance += _value;
    }
}
```

把migrate資料夾下的\_deploy\_contracts.js改為如下

```
var HelloWorld = artifacts.require("./HelloWorld.sol");

module.exports = function(deployer) {
  deployer.deploy(HelloWorld);
};
```

之後

```
truffle compile  => truffle migrate  => truffle console
```

> compile是把sol compile之後會多出json檔案，migrate是把contract部署到RPC上

查看餘額

```
HelloWorld.deployed().then(ins => ins.balance().then(a => console.log(a)))
```

存錢

```
HelloWorld.deployed().then(a => {a.deposit(200)})
```

注意事項:  
1.

> Truffle更改了API
>
> ```
> ...deployed()後面要用then來接
> ```
>
> ex:
>
> ```
> HelloWorld.deployed().then(a => console.log(a.address))
> ```
>
> ```
> HelloWorld.deployed().then(a => console.log(a.balance().then(console.log)))
> ```
>
> [http://ethereum.stackexchange.com/questions/11935/problem-with-truffle-console-cannot-read-property-call-of-undefined](http://ethereum.stackexchange.com/questions/11935/problem-with-truffle-console-cannot-read-property-call-of-undefined)

2.

> 檔案sol的名稱要跟contract名稱相同

3.

> 如果想更改已部署到RPC上的contract要輸入  
> `truffle migrate --reset`



## 使用truffle box

官方有內建幾個box，算是模板，可供下載

```
truffle unbox <package>
```

但如果搭配testrpc，記得更改設定的port以及寫gasLimit，不然可能會出錯

```json
module.exports = {
  // See <http://truffleframework.com/docs/advanced/configuration>
  // for more about customizing your Truffle configuration!
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8545,
      network_id: "*", // Match any network id
      gas: 4712388
    }
  }
};
```



