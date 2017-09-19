#### 有關從區塊鏈上移除contract

> 合約只能disable  之後所有變數都會歸0，但合約本身不可完全從區塊鏈上刪除 
>
> 並且還是可以往合約地址發送交易以及執行function 但發過去的ether會就此消失

參考以下說明

[https://ethereum.stackexchange.com/questions/17504/how-can-a-contract-destroy-other-contract-suicide-accounts](https://ethereum.stackexchange.com/questions/17504/how-can-a-contract-destroy-other-contract-suicide-accounts)

[https://ethereum.stackexchange.com/questions/315/why-are-suicides-used-in-contract-programming](https://ethereum.stackexchange.com/questions/315/why-are-suicides-used-in-contract-programming)

[https://www.reddit.com/r/ethereum/comments/3ilpvb/what\_happens\_when\_you\_send\_ether\_to\_a\_postsuicide/](https://www.reddit.com/r/ethereum/comments/3ilpvb/what_happens_when_you_send_ether_to_a_postsuicide/)

[http://solidity.readthedocs.io/en/develop/miscellaneous.html?highlight=destroy](http://solidity.readthedocs.io/en/develop/miscellaneous.html?highlight=destroy)

