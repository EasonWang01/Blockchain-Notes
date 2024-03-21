# Hardhat 教學

類似 Truffle 為一個智能合約開發 SDK。

{% embed url="https://hardhat.org/" %}

### 安裝與初始化

```
mkdir hardhat-test && cd hardhat-test
npx hardhat init
```

### 專案結構

![](<../.gitbook/assets/截圖 2021-09-14 下午12.09.39.png>)

必須照著官方規定取名資料夾，分別為放合約以及部署script，與測試檔案，以及網路等 config。

[https://hardhat.org/guides/project-setup.html#sample-hardhat-project](https://hardhat.org/guides/project-setup.html#sample-hardhat-project)

### 在本地測試與部屬

1. 啟動本地節點

> \[可選] 如果沒有啟動節點與指定節點，預設都會使用 in-memory instance of Hardhat Network

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

## 使用 Mainnet fork 測試

> 使用 mainnet 特定 block data 來測試合約

例如以下獲取主網特定地址的 USDC 餘額

```javascript
const { ethers } = require("hardhat");
const { expect } = require("chai");

describe("USDC Balance Check", function () {
  it("Should return the USDC balance of the address", async function () {
    const usdcAddress = "0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48"; // USDC Contract Address
    const addressToCheck = "0x7713974908Be4BEd47172370115e8b1219F4A5f0"; // Address whose balance you want to check

    // USDC Contract ABI (Simplified; use the full ABI from Etherscan)
    const usdcAbi = ["function balanceOf(address owner) view returns (uint256)"];
    
    // Connect to the USDC contract
    const usdcContract = new ethers.Contract(usdcAddress, usdcAbi, ethers.provider);

    // Fetch the balance
    const balance = await usdcContract.balanceOf(addressToCheck);

    // Log the balance (optional)
    console.log("USDC Balance:", balance.toString());


    // 也可手動改變地址 ETH 餘額
    const mainnetAccount = addressToCheck; // Replace with a mainnet account address
    await hre.network.provider.request({
      method: "hardhat_impersonateAccount",
      params: [mainnetAccount],
    });
    await hre.network.provider.send("hardhat_setBalance", [
      mainnetAccount,
      "0x8AC7230489E80000000", // 40960 ETH
    ]);

    const ethBalance = await ethers.provider.getBalance(addressToCheck);

    // Log the balance (optional)
    console.log("ETH Balance:", ethers.formatEther(ethBalance), "ETH");

    // Assert (for example, checking if the balance is greater than a certain amount)
    expect(balance).to.be.gt(ethers.parseUnits("100", 6)); // Check if balance is greater than 100 USDC
  });
});
```

## 測試檔案內設置區塊時間

```typescript
const { ethers } = require("hardhat");

// Fast forward time by 7 days for testing
await ethers.provider.send("evm_increaseTime", [7 * 24 * 60 * 60]);
await ethers.provider.send("evm_mine");
```

## 部署合約

要先去 hardhat.config.ts 檔案設置網路設置

```typescript
import { HardhatUserConfig } from "hardhat/config";
import "@nomicfoundation/hardhat-toolbox";
require('dotenv').config()

const config: HardhatUserConfig = {
  solidity: "0.8.24",
  networks: {
    bevmTestnet: {
      url: "<rpc url>",
      accounts: [`${process.env.PRIVATE_KEY}`]
    }
  }
};

export default config;
```

部署腳本 scripts/deploy.ts

```typescript
import { ethers } from "hardhat";

async function main() {
  const EthStaker = await ethers.deployContract("EthStaker");

  await EthStaker.waitForDeployment();

  console.log(
    `EthStaker deployed to ${EthStaker.target}`
  );
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```

## Etherscan 驗證合約

{% embed url="https://hardhat.org/plugins/nomiclabs-hardhat-etherscan.html" %}

## 測試單一檔案

可使用：[https://hardhat.org/plugins/hardhat-watcher.html](https://hardhat.org/plugins/hardhat-watcher.html)

會監聽檔案改變，自動跑測試，並且可在 config 寫要執行哪個檔案

## Hardhat 部署到正式網或測試網

可使用 hardhat deploy plugin

[https://github.com/wighawag/hardhat-deploy#hardhat-deploy-in-a-nutshell](https://github.com/wighawag/hardhat-deploy#hardhat-deploy-in-a-nutshell)

## 相關連結

範例專案：[https://github.com/symfoni/hardhat-react-boilerplate](https://github.com/symfoni/hardhat-react-boilerplate)

## 常見問題

1.Error: call revert exception

```
通常是直接使用 hardhat vm 部署與測試合約所產生，可改用 hardhat localhost node 試試
```

2.Error HH12: Trying to use a non-local installation of Hardhat

{% embed url="https://stackoverflow.com/a/69973620/4622645" %}

3.TypeError: unsupported addressable value (argument="target", value=null, code=INVALID\_ARGUMENT, version=6.11.1)

v6 之後要用 .target 取代 .address

```typescript
SimplifiedNFT1155 = await ethers.getContractFactory("SimplifiedNFT1155");
simplifiedNFT1155 = await SimplifiedNFT1155.deploy("baseURI/");

NFTStaker = await ethers.getContractFactory("NFTStaker");
nftStaker = await NFTStaker.deploy(simplifiedNFT1155.target);
```
