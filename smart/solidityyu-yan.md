# \#Event

通常當作log使用

Ex:

```
event log_string(bytes32 log); // Event

function c() returns(address){
    log_string("Hello World!");
}
```

之後執行c\(\) 後到etherscan可看到如下

![](/assets/螢幕快照 2017-09-19 下午10.41.36.png)

[https://karl.tech/learning-solidity-part-1-deploy-a-contract/](https://karl.tech/learning-solidity-part-1-deploy-a-contract/)

#### 

#### 

#### 

# \#有關從區塊鏈上移除contract

> 合約只能disable  之後所有變數都會歸0，但合約本身不可完全從區塊鏈上刪除
>
> 並且還是可以往合約地址發送交易以及執行function 但發過去的ether會就此消失

Ex:

```
function kill() { if (msg.sender == owner) selfdestruct(owner);}
```

參考以下說明

[https://ethereum.stackexchange.com/questions/17504/how-can-a-contract-destroy-other-contract-suicide-accounts](https://ethereum.stackexchange.com/questions/17504/how-can-a-contract-destroy-other-contract-suicide-accounts)

[https://ethereum.stackexchange.com/questions/315/why-are-suicides-used-in-contract-programming](https://ethereum.stackexchange.com/questions/315/why-are-suicides-used-in-contract-programming)

[https://www.reddit.com/r/ethereum/comments/3ilpvb/what\_happens\_when\_you\_send\_ether\_to\_a\_postsuicide/](https://www.reddit.com/r/ethereum/comments/3ilpvb/what_happens_when_you_send_ether_to_a_postsuicide/)

[http://solidity.readthedocs.io/en/develop/miscellaneous.html?highlight=destroy](http://solidity.readthedocs.io/en/develop/miscellaneous.html?highlight=destroy)

# \# external 與 public  與 internal 與private

[https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices](https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices)

```
Functions can be specified as being external, public, internal or private, where the default is public. For state variables, external is not possible and the default is internal.

external:
External functions are part of the contract interface, which means they can be called from other contracts and via transactions. An external function f cannot be called internally (i.e. f() does not work, but this.f() works). External functions are sometimes more efficient when they receive large arrays of data.

public:
Public functions are part of the contract interface and can be either called internally or via messages. For public state variables, an automatic getter function (see below) is generated.

internal:
Those functions and state variables can only be accessed internally (i.e. from within the current contract or contracts deriving from it), without using this.

private:
Private functions and state variables are only visible for the contract they are defined in and not in derived contracts.
```



# \#**return **or **throw**

> The former will cost less gas but it can be more headache as any changes you did to the contract so far will be kept. In the other hand, 'throw' will cancel all contract execution, revert any changes that transaction could have made and the sender will lose all ether he sent for gas. But since the Wallet can detect that a contract will throw, it always shows an alert, therefore preventing any ether to be spent at all.

return可以花費較少gas 但在return前的code會執行 ，而throw則會把之前的也取消



