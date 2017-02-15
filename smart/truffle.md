
https://github.com/ConsenSys/truffle
```
npm install -g truffle
```

####1.

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
把migrate資料夾下的_deploy_contracts.js改為如下

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
>compile是把sol compile之後會多出json檔案，migrate是把contract部署到RPC上

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
>Truffle更改了API
```
...deployed()後面要用then來接
```

>ex:
```
HelloWorld.deployed().then(a => console.log(a.address))
```
```
HelloWorld.deployed().then(a => console.log(a.balance().then(console.log)))﻿
```
http://ethereum.stackexchange.com/questions/11935/problem-with-truffle-console-cannot-read-property-call-of-undefined

2.
>檔案sol的名稱要跟contract名稱相同