#簡介:
以太坊（英語：Ethereum）是一個開源且具有智慧合約區塊鏈平台，設計上是為了解決比特幣擴展性不足的問題，以太坊區塊鏈上的代幣稱為以太幣（Ether），代碼為ETH

合約:

>合約是一個活在以太坊系統內的自動代理人，他有一個自己的乙太幣地址，當用戶向合約的地址發送一筆交易後，該合約就被激活，然後根據交易中的額外信息，合約會運行自身的代碼，最後返回一個結果，這個結果可能是從合約的地址發出另外一筆交易



#常見名詞:

Dapp:
>A Dapp (‘decentralized app’) consists of two parts: a frontend, written in HTML, and a backend (think of it as the ‘database’ for your frontend).


geth:

>geth is the the command line interface for running a full ethereum node implemented in Go. 

JSON-PRC:
https://github.com/ethereum/wiki/wiki/JSON-RPC
>簡單說即為一個會回應json格式的server

https://github.com/ethereum/wiki/wiki/JSON-RPC


IPC:

>為系統中兩個兩個執行緒互相傳遞資料的方法

https://zh.wikipedia.org/wiki/%E8%A1%8C%E7%A8%8B%E9%96%93%E9%80%9A%E8%A8%8A

http://albert-oma.blogspot.tw/2013/06/linux-ipc.html

Ethash:

>以太幣(ether)的挖礦算法叫做Ethash, 又名Dashimoto (Dagger-Hashimoto)，是Hashimoto算法結合Dagger之後產成的一個變種

Dag:
https://github.com/ethereum/wiki/blob/master/Dagger-Hashimoto.md


EVM:
>以太坊虛擬機，輕量級虛擬機環境，是以太坊中智能合約的運行環境

Gas:
>燃料，每執行一條合約指令會消耗一定的燃料，當某個交易還未執行結束，而燃料消耗完時，合約執行終止並回滾(rollback)狀態，可與ether進行換算，但不可交易。