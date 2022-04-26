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

## 相關利息參數

{% embed url="https://ian.pw/posts/2020-12-20-understanding-compound-protocols-interest-rates" %}

{% embed url="https://www.gushiciku.cn/pl/gXE6/zh-tw" %}

## 部署後可呼叫方法

提款：

cToken -> `_reduceReserves`

利息參數：

https://observablehq.com/@jflatow/compound-interest-rates

後續設置:

&#x20;interestRateModel: cToken -> `_setInterestRateModel`

升級 cToken:

&#x20;`cTokenDelegator._setImplementation(address(cTokenDelegate), false, 0x)`

升級 comptrolller:&#x20;

```
unitroller._setPendingImplementation(<new comproller address>); 
unitroller._become();
```

## 相關事項

1.新建 cToken 市場幣種之後必須要兩個人以上去 cToken supply 後才能去執行 redeem 或 borrow，不然 utilizationRate function 計算時 reserve 變 0 會產生 Error，整個市場變成不能用。

> 解決方法為轉一些 underlyingAsset 到 cToken address

2.部署新幣時記得要先讓 oracle 設置價格後才能設置 collateral factor ，不然執行 `_setCollateralFactor` 會有 error

![](<../../.gitbook/assets/截圖 2022-04-25 上午11.17.37.png>)

3.可以使用 cToken.isDeprecated 來判斷某個 cToken 是否已沒在作用，\_setCollateralFactor 與\_setReserveFactor 為 0 以及 \_setBorrowPaused 為 true 即可。

![](<../../.gitbook/assets/截圖 2022-04-25 上午11.21.55.png>)
