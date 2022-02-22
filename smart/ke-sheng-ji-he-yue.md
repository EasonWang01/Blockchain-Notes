# 可升級合約

使用 delegate call 搭配 solidity 的 fallback function 傳入 calldata。呼叫時會呼叫 proxy contract 然後指向實際的邏輯合約。

{% embed url="https://docs.openzeppelin.com/learn/upgrading-smart-contracts" %}

### 原理

[https://medium.com/coinmonks/upgradeable-proxy-contract-from-scratch-3e5f7ad0b741](https://medium.com/coinmonks/upgradeable-proxy-contract-from-scratch-3e5f7ad0b741)
