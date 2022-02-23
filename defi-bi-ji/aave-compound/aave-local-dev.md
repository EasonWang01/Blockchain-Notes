# AAVE local dev

1.獲取 Kovan 測試幣：

{% embed url="https://faucets.chain.link" %}

2.架設 aave-ui

{% embed url="https://github.com/aave/aave-ui" %}

3.架設好連線錢包，這邊記得選測試網路

![](<../../.gitbook/assets/截圖 2022-02-23 下午3.37.30.png>)

選完後會執行前端的以下

![](<../../.gitbook/assets/截圖 2022-02-23 下午3.38.05.png>)



4.執行 deposit

![](<../../.gitbook/assets/截圖 2022-02-23 下午3.43.45.png>)

這時互動的合約地址如下：（如果是存 ETH 會指向 wETH 合約，其他的指向 LENDING\_POOL 合約）

![](<../../.gitbook/assets/截圖 2022-02-23 下午3.43.17.png>)





## 合約部署相關

以下合約設置其他 aave 合約的相關地址

[https://github.com/aave/aave-protocol/blob/master/contracts/configuration/LendingPoolAddressesProvider.sol](https://github.com/aave/aave-protocol/blob/master/contracts/configuration/LendingPoolAddressesProvider.sol)

#### 有關 liquidation&#x20;

liquidation 的觸發可以是任何人，只要你有發現其他人帳戶的某個資產的 health factor < 1 即可觸發，所以通常會是有個機器人程式定期去掃描每個人的借貸資產的 health factor

[https://docs.aave.com/developers/v/1.0/tutorials/liquidations](https://docs.aave.com/developers/v/1.0/tutorials/liquidations)

{% embed url="https://github.com/aave/protocol-v2/blob/baeb455fad42d3160d571bd8d3a795948b72dd85/contracts/adapters/FlashLiquidationAdapter.sol#L50" %}

health factor < 1 執行 liquidation&#x20;

{% embed url="https://github.com/aave/protocol-v2/blob/baeb455fad42d3160d571bd8d3a795948b72dd85/test-suites/test-amm/uniswapAdapters.flashLiquidation.spec.ts#L27" %}

health factor 合約計算方式

{% embed url="https://github.com/aave/protocol-v2/blob/baeb455fad42d3160d571bd8d3a795948b72dd85/contracts/protocol/libraries/logic/GenericLogic.sol#L242" %}

flashLoan 的 liquidate function call 順序

flashLoan -> executeOperation -> \_liquidateAndSwap -> liquidationCall -> calculateHealthFactorFromBalances
