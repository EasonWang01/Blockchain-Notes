# Hardhat 教學

類似 Truffle 為一個智能合約開發 SDK。

{% embed url="https://hardhat.org/getting-started/" %}

### 專案結構

![](<../.gitbook/assets/截圖 2021-09-14 下午12.09.39.png>)

必須照著官方規定取名資料夾，分別為放合約以及部署script，與測試檔案，以及網路等 config。

[https://hardhat.org/guides/project-setup.html#sample-hardhat-project](https://hardhat.org/guides/project-setup.html#sample-hardhat-project)

### 在本地測試與部屬

1. 啟動本地節點

> 如果沒有啟動節點與指定節點，預設都會使用 in-memory instance of Hardhat Network

```
npx hardhat node
```

2.部署合約

```
npx hardhat run --network localhost scripts/deploy.js
```

3.使用 console 測試功能

```
npx hardhat console --network localhost
```

之後輸入

```
const Greeter = await ethers.getContractFactory("Greeter");
```

```
const greeter = await Greeter.attach(<剛才部署本地的合約地址>);
```

```
await greeter.greet()
```

### 寫測試檔案

test/test.js

```javascript
const { expect } = require("chai");
const { ethers } = require("hardhat");

describe("Greeter12", function () {
  it("Should return the new greeting once it's changed", async function () {
    
    const Greeter = await ethers.getContractFactory("Greeter");
    // const greeter = await Greeter.deploy("Hello, world!");
    // await greeter.deployed();

    //expect(await greeter.greet()).to.equal("Hello, world!");
    const greeter = await Greeter.attach("0x5FbDB2315678afecb367f032d93F642f64180aa3");

    const setGreetingTx = await greeter.setGreeting("Hola, mundo!");

    // wait until the transaction is mined
    await setGreetingTx.wait();
    const a = await greeter.greet()
    //console.log(a)

    expect(await greeter.greet()).to.equal("Hola, mundo!");
  });
});

```

之後輸入

```
npx hardhat test --network localhost
```

## Artifacts

部署合約或 compile 後會產生此資料夾，裡面包含一些 json 檔案，為合約的 ABI

{% embed url="https://hardhat.org/guides/compile-contracts.html#compiling-your-contracts" %}

## Etherscan 驗證合約

{% embed url="https://hardhat.org/plugins/nomiclabs-hardhat-etherscan.html" %}

## 測試單一檔案

可使用：[https://hardhat.org/plugins/hardhat-watcher.html](https://hardhat.org/plugins/hardhat-watcher.html)

會監聽檔案改變，自動跑測試，並且可在 config 寫要執行哪個檔案

## 常見問題

1.Error: call revert exception

```
通常是直接使用 hardhat vm 部署與測試合約所產生，可改用 hardhat localhost node 試試
```

2.Error HH12: Trying to use a non-local installation of Hardhat

[https://stackoverflow.com/a/69973620/4622645](https://stackoverflow.com/a/69973620/4622645)
