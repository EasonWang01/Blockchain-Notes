
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


####2.
Truffle更改了API

```
...deployed()後面要用then來接
```
ex:

```
HelloWorld.deployed().then(a => console.log(a.address))

HelloWorld.deployed().then(a => console.log(a.balance().then(console.log)))﻿
```

http://ethereum.stackexchange.com/questions/11935/problem-with-truffle-console-cannot-read-property-call-of-undefined

