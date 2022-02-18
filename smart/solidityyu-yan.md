# solidity 語言

## Global variable

內建在 solidity 可使用的

{% embed url="https://docs.soliditylang.org/en/v0.4.24/units-and-global-variables.html" %}

## Cheatsheet 對照表

[https://reference.auditless.com/cheatsheet/](https://reference.auditless.com/cheatsheet/)

## Event

通常當作log使用

Ex:

```
event log_string(bytes32 log); // Event

function c() returns(address){
    log_string("Hello World!");
}
```

之後執行c() 後到etherscan可看到如下

![](<../.gitbook/assets/螢幕快照 2017-09-19 下午10.41.36.png>)

[https://karl.tech/learning-solidity-part-1-deploy-a-contract/](https://karl.tech/learning-solidity-part-1-deploy-a-contract/)

### 通常用大寫命名

```
event FrozenFunds(address target, bool frozen);
```

## 有關從區塊鏈上移除contract

> 合約只能disable 之後所有變數都會歸0，但合約本身不可完全從區塊鏈上刪除
>
> 並且還是可以往合約地址發送交易以及執行function 但發過去的ether會就此消失

Ex:

```
function kill() { if (msg.sender == owner) selfdestruct(owner);}
```

參考以下說明

[https://ethereum.stackexchange.com/questions/17504/how-can-a-contract-destroy-other-contract-suicide-accounts](https://ethereum.stackexchange.com/questions/17504/how-can-a-contract-destroy-other-contract-suicide-accounts)

[https://ethereum.stackexchange.com/questions/315/why-are-suicides-used-in-contract-programming](https://ethereum.stackexchange.com/questions/315/why-are-suicides-used-in-contract-programming)

[https://www.reddit.com/r/ethereum/comments/3ilpvb/what\_happens\_when\_you\_send\_ether\_to\_a\_postsuicide/](https://www.reddit.com/r/ethereum/comments/3ilpvb/what\_happens\_when\_you\_send\_ether\_to\_a\_postsuicide/)

[http://solidity.readthedocs.io/en/develop/miscellaneous.html?highlight=destroy](http://solidity.readthedocs.io/en/develop/miscellaneous.html?highlight=destroy)

## external 與 public  與 internal 與private

[https://ethereum.stackexchange.com/a/19391/30691](https://ethereum.stackexchange.com/a/19391/30691)

[http://solidity.readthedocs.io/en/develop/contracts.html#visibility-and-getters](http://solidity.readthedocs.io/en/develop/contracts.html#visibility-and-getters)

[https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices](https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices)

```
Functions can be specified as being external, public, internal or private, where the default is public. For state variables, external is not possible and the default is internal.

external:  只可以給外面的contract呼叫，不可內部呼叫
External functions are part of the contract interface, which means they can be called from other contracts and via transactions. An external function f cannot be called internally (i.e. f() does not work, but this.f() works). External functions are sometimes more efficient when they receive large arrays of data.

public:   都可呼叫
Public functions are part of the contract interface and can be either called internally or via messages. For public state variables, an automatic getter function (see below) is generated.

internal: 跟private類似，但如果是繼承該contract的contract也可以呼叫
Those functions and state variables can only be accessed internally (i.e. from within the current contract or contracts deriving from it), without using this.

private:  只有同contract才可呼叫
Private functions and state variables are only visible for the contract they are defined in and not in derived contracts.
```

## View, Pure, Const

> 修改的意思代表會去更改合約中其他變數的值。

View: 可讀取，但不可修改

Pure: 不可讀取，不可修改

const: 可讀取，但不可修改，不可加在function上

## **return** or **throw**

> The former will cost less gas but it can be more headache as any changes you did to the contract so far will be kept. In the other hand, 'throw' will cancel all contract execution, revert any changes that transaction could have made and the sender will lose all ether he sent for gas. But since the Wallet can detect that a contract will throw, it always shows an alert, therefore preventing any ether to be spent at all.

return可以花費較少gas 但在return前的code會執行 ，而throw則會把之前的也取消

## Modifier

> \_的意思為繼承這個modifier的function會先跑完寫在\_modifier __ ; 之前的東西才繼續該function

```
modifier onlyOwner {
        require(msg.sender == owner);
        _;
    }
```

用法

```
function transferOwnership(address newOwner) onlyOwner {
        owner = newOwner;
    }
```

## Require() , assert()

> 用來確認這個行code正常執行 如果不正常就會拋出錯誤 直接中斷並取消整個function

[http://solidity.readthedocs.io/en/develop/control-structures.html?highlight=require#error-handling-assert-require-revert-and-exceptions](http://solidity.readthedocs.io/en/develop/control-structures.html?highlight=require#error-handling-assert-require-revert-and-exceptions)

## Mapping

mapping 可以想像是一個hashTable，即 key 對應 value

```c
struct Person {
    bytes32 name;
    uint age;
}

mapping (address => Person) person;
```

`person` 為mapping類型，它的 key 是address 類型， value是Person 之struct類型

## payable

```
function buyTicket(uint amount) payable {
}
```

包含payable的function才可以接受Ether

## selfdestruct

將智能合約永久從區塊鏈上移除，並且將接到的Ether傳給selfdestruct之參數

```
selfdestruct(address)
```

## Fallback functions

如果呼叫的function不存在於智能合約上，則 EVM 會自動去執行fallback function

> 類似於匿名函數的寫法

```
function() {
     ....
}
```

[https://github.com/ConsenSys/Ethereum-Development-Best-Practices/wiki/Fallback-functions-and-the-fundamental-limitations-of-using-send()-in-Ethereum-&-Solidity](https://github.com/ConsenSys/Ethereum-Development-Best-Practices/wiki/Fallback-functions-and-the-fundamental-limitations-of-using-send\(\)-in-Ethereum-&-Solidity)

## inheritance繼承

```
contract man {
 .... 
}

contract superman is man {
 .... 
}
```

## interface介面

> 如果有寫interface，繼承的contract必須要實作，否則無法編譯

```
contract man {
  function run() returns (strings); 
}

contract superman is man {
  function run() returns (strings) {
    return "I'm running";
  }
}
```

另一種寫法

```
interface runningMan {
  function swim() returns (string);
}

contract superman is swim {
  function swim() returns (string) {
    return "I'm swimming";
  }
}
```

## Contract轉帳Ether

```
msg.sender.transfer(100) 

msg.sender.send(100)

// msg.sender 可換為其他address
```

[https://github.com/ethereum/solidity/issues/610](https://github.com/ethereum/solidity/issues/610)

## 呼叫其他Contract

先部署Calculate contract 到鏈上

```
contract calculate {
  function add (int a, int b) returns (int) {
    return a + b
  }
}
```

假設其地址為0xfEDDF8DB160Dcb85f793bfEe734352760C4AB96a

之後呼叫其contract

```
contract computer {
  calculate calc = new calculate(0xfEDDF8DB160Dcb85f793bfEe734352760C4AB96a);
  function 2Add3() constant returns (int) {} {
    calc.add(2, 3);
  }
}
```

## memory/storage

類似pass by value 與 pass by reference

function之參數預設均為memory，使用storage如下用法

```
uint[] abc = [1, 2, 3];

function test(uint[] storage _abc) internal {
    _abc[0] = 0;
}

// abc變為[0, 2, 3]
```

> function只能為internal或private

## 參數傳入struct或是返回struct

可參考：[https://github.com/ethereum/web3.js/issues/1148](https://github.com/ethereum/web3.js/issues/1148)

> 目前會出現：error: Failed to decode output: Error: Unsupported or invalid type: tuple 錯誤

## abi.encodePacked

把參數的 array 串接起來為 bytes，如果參數有 string 會轉為 16進位 hex

```javascript
abi.encodePacked(msg.sender, "t");
// 0x5b38da6a701c568545dcfcb03fcb875f56beddc474
// msg sender: 0x5b38da6a701c568545dcfcb03fcb875f56beddc4
```

## Call v.s Delegatecall

[https://ethereum.stackexchange.com/questions/3667/difference-between-call-callcode-and-delegatecall](https://ethereum.stackexchange.com/questions/3667/difference-between-call-callcode-and-delegatecall)
