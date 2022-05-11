# 可升級合約

使用 delegate call 搭配 solidity 的 fallback function 傳入 calldata。呼叫時會呼叫 proxy contract 然後指向實際的邏輯合約。

{% embed url="https://docs.openzeppelin.com/learn/upgrading-smart-contracts" %}

### 原理

{% embed url="https://medium.com/coinmonks/upgradeable-proxy-contract-from-scratch-3e5f7ad0b741" %}

## DelegateCall 用法

```javascript
contract test {
    uint256 public result;
    function add(uint256 a, uint256 b) public returns (uint256) {
        result = a + b;
        return result;
    }
}

contract test1 {
    function addValues(address test, uint256 a, uint256 b) public returns (uint256) {
        (bool success, bytes memory result) = test.delegatecall(abi.encodeWithSignature("add(uint256,uint256)", a, b));
        return abi.decode(result, (uint256));
    }
}
```

> Call v.s delegateCall:
>
> &#x20;[https://ethereum.stackexchange.com/a/3672/30691](https://ethereum.stackexchange.com/a/3672/30691)
>
> encodeWithSignature v.s encodeWithSelector: [https://ethereum.stackexchange.com/q/91826/30691](https://ethereum.stackexchange.com/q/91826/30691)
