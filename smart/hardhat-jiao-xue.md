# Hardhat 教學

類似 Truffle 為一個智能合約開發 SDK。

{% embed url="https://hardhat.org/getting-started/" %}

### 在本地測試與部屬

1. 啟動本地節點

```text
npx hardhat node
```

2.部署合約

```text
npx hardhat run --network localhost scripts/deploy.js
```

3.使用 console 測試功能

```text
npx hardhat console --network localhost
```

之後輸入

```text
const Greeter = await ethers.getContractFactory("Greeter");
```

```text
const greeter = await Greeter.attach(<剛才部署本地的合約地址>);
```

```text
await greeter.greet()
```

