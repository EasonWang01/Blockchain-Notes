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

