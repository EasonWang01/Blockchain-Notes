#簡介:
以太坊（英語：Ethereum）是一個開源且具有智慧合約區塊鏈平台，設計上是為了解決比特幣擴展性不足的問題，以太坊區塊鏈上的代幣稱為以太幣（Ether），代碼為ETH

合約:

>合約是一個活在以太坊系統內的自動代理人，他有一個自己的乙太幣地址，當用戶向合約的地址發送一筆交易後，該合約就被激活，然後根據交易中的額外信息，合約會運行自身的代碼，最後返回一個結果，這個結果可能是從合約的地址發出另外一筆交易

查看交易資訊:
https://etherchain.org/

#常見名詞:

Dapp:
>A Dapp (‘decentralized app’) consists of two parts: a frontend, written in HTML, and a backend (think of it as the ‘database’ for your frontend).
以太坊社區把基於智能合約的應用稱為去中心化的應用程序(Decentralized App)。DApp的目標是(或者應該是)讓你的智能合約有一個友好的界面

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

#相關實用網站

1、以太坊官方網站：https://ethereum.org/

2、以太坊原始碼（官方）：https://github.com/ethereum/

3、以太坊說明文件（官方）：http://www.ethdocs.org/en/latest/index.html

4、以太坊網路狀態（官方）：https://ethstats.Net /

5、以太坊相關工具與資源網站（官方）：http://ether.fund/

6、Solidity說明文件（官方）：http://solidity.readthedocs.io/en/latest/

7、以太坊網路掃描（官方）：http://etherscan.io/

8、以太坊官方部落格：https://blog.ethereum.org/

9、以太坊wiki百科：https://github.com/ethereum/wiki/wiki

10、以太坊中文愛好者網站：http://ethfa​​ns.org/

11、以太坊的gitter的實時交流網站：https://gitter.im/orgs/ethereum/rooms    

12、以太坊的官方論壇：https://forum.ethereum.org/

13、以太坊第三方強大的IDE（Solidity IDE）：https://live.ether.camp/

14、以太坊開發框架Truffle說明書：http://truffle.readthedocs.io/en/latest/

15、以太坊開發框架dapple說明書：http://dapple.readthedocs.io/en/master/

16、以太坊官方推薦開發框架Meteor說明書：https://github.com/ethereum/wiki/wiki/Dapp-using-Meteor

#Testnet

取得免費的ether 
https://ropsten.ether.camp/transactions
http://faucet.ropsten.be:3001/
查看區塊情況  https://testnet.etherscan.io/

# 共識機制介紹

POW（Proof of Work，工作證明）是指獲得多少貨幣，取決於你挖礦貢獻的工作量，即為一般的礦機或顯卡挖礦

POS（Proof of Stake，股權證明）根據你持有貨幣的量和時間進行利息分配的制度，在POS模式下，挖礦收益正比於你的幣齡，而與電腦的計算性能無關，幣齡即為持有乙太幣的時間

PoA ( Proof of Authority)
有別於PoW (Proof-of-Work)需要解數學難題來產生block，PoA是依靠預設好的Authority nodes，負責產生block。
可設定Authority node數量。可指定產生block的時間等
