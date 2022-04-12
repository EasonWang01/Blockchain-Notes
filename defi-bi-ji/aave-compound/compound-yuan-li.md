# Compound 原理

## 合約架構

1. 主體以 comptroller 為核心，設置所有市場 supportMarket 以及 CollateralFactor、LiquidationIncentive、 CloseFactor、PriceOracle 等等。

&#x20; 2\. cToken 部署方式類似如下是使用 CErc20Delegate 與 CErc20Delegator&#x20;

```javascript
const cBUSDDelegatee = await (await ethers.getContractFactory('CErc20Delegate')).deploy()
const cBUSDDelegator = await (await ethers.getContractFactory('CErc20Delegator')).deploy(
  busd.address, comptroller.address, interestRateModel.address, bigInt(1), 'cToken cBUSD', 'cBUSD', 8, deployer.address, cBUSDDelegatee.address, '0x'
 )
const cbusd = await ethers.getContractAt('CErc20Delegate', cBUSDDelegator.address)
```

&#x20;3\. 設置抵押品的方式為：擁有對應的 cToken underlying asset 後使用 `mint` 來獲得對應的 cToken，之後使用 enterMarket 來把 cToken 設置為抵押物，抵押之後 account 的 liquidity 會增加，即可借貸。

&#x20;4\. 之後使用 cToken 的 borrow 後即可借出對應的 token。（例如 cBTC 執行 borrow 後可借出 BTC）

&#x20;5\. 獲取帳戶的當前狀態：

`comptroller` 合約調用 `getAccountLiquidity()`，回傳三個參數分別為 `(error, liquidity, shortfall)`&#x20;

* 第一個參數 0 表示 success&#x20;
* 第二個參數代表 account liquidity 可用流動性 (還可借款多少金額 = 所有 cToken 加總（ cToken 抵押品價值 \* cToken collateral factor))&#x20;
* 第三個參數 shortfall 如果不為零代表可被清算(需要補足多少金額)，有 shortfall 時 liquidity 一定為 0

## Compound 清算機制

[CEther.sol](https://github.com/compound-finance/compound-protocol/blob/ae4388e780a8d596d97619d9704a931a2752c2bc/contracts/CEther.sol) 或 [CErc20.sol](https://github.com/compound-finance/compound-protocol/blob/20abad28055a2f91df48a90f8bb6009279a4cb35/contracts/CErc20.sol) 會呼叫 [CToken](https://github.com/compound-finance/compound-protocol/blob/b9b14038612d846b83f8a009a82c38974ff2dcfe/scenario/src/Contract/CToken.ts) 合約的 liquidateBorrow();
