#簡介:
以太坊（英語：Ethereum）是一個開源且具有智慧合約區塊鏈平台，設計上是為了解決比特幣擴展性不足的問題，以太坊區塊鏈上的代幣稱為以太幣（Ether），代碼為ETH

合約:

>合約是一個活在以太坊系統內的自動代理人，他有一個自己的乙太幣地址，當用戶向合約的地址發送一筆交易後，該合約就被激活，然後根據交易中的額外信息，合約會運行自身的代碼，最後返回一個結果，這個結果可能是從合約的地址發出另外一筆交易


#安裝:

這邊我們會使用`Docker`，並使用ethereum相關的container來進行
https://hub.docker.com/r/ethereum/client-go/

所以須先安裝好`Docker`https://docs.docker.com/engine/installation/
並且執行

再來開啟terminal

1.加入以太坊的public network
```
docker run -d --name ethereum-node \
    -v $HOME/.ethereum:/root \
    -p 8545:8545 -p 30303:30303 \
    ethereum/client-go --fast --cache=512
```
執行attach
```
docker exec -ti ethereum-node geth attach
```
2.加入以太坊的test network
```
docker run -d --name ethereum-node \
    -v $HOME/.ethereum:/root \
    -p 8545:8545 -p 30303:30303 \
    ethereum/client-go --testnet --fast --cache=512
```
執行attach

```
docker exec -ti ethereum-node \
    geth attach ipc:/root/.ethereum/testnet/geth.ipc
```

查看LOG
```
docker logs ethereum-node
```


>attach的意思是在terminal啟動一個javascript的相關以太坊API執行環境
https://github.com/ethereum/wiki/wiki/JavaScript-API


但我們這邊主要教學部分為Private chain
https://github.com/vertigobr/ethereum

使用
```
git clone https://github.com/vertigobr/ethereum.git

cd ethereum
```


我們可以到他的repo中點選`Dockerfile`

看到

```
ENV GEN_NONCE="0xeddeadbabeeddead" \

    NET_ID=1981
```


這兩個分別是創世區塊的識別ID與private network的識別ID，所以未來加入其他節點這兩個都要與其他節點的值相同才會加入，預設我們不用去改他


我們也可以用`-e`來指定執行docker時的環境變數
```
docker run -e GEN_NONCE='stag0x2222ing' 
```

講解其中的一些.sh程式
```
bootnode.sh: 執行Ethereum bootnode 啟動節點;

runnode.sh: 執行Ethereum 非挖礦節點;

runminer.sh: 執行Ethereum 挖礦節點;

showpeers.sh: 顯示所有連線到此節點的節點

killall.sh: 將會執行"docker stop" 與 "docker rm" ;

wipeall.sh: 將會執行"docker stop" 與 "docker rm" 並且清空 volume folders.
```

進入到資料夾後使用

1.啟動起始節點
```
./bootnode.sh
```
使用`docker logs ethereum-bootnode`查看log

查看剛才的boot節點
```
./getbootnodeurl.sh
```

2.再來啟動另一個非mining的節點

```
./runnode.sh node1
```
查看logs
```
docker logs ethereum-node1
```
這時發現這個node沒找到任何其他節點，只出現以下，像bootnode不停查詢是否有其他節點，所以他覺得滿孤單
```
dial tcp 172.17.0.2:30301
與
seed node
```

3.新增第二個非mining的節點

```
./runnode.sh node2
```

這時我們回去察看node1的log，可看到類似如下
```
Peer e4a6e56cf9096420 [eth/63]: peer connected [Geth/v1.4.11-stable/linux/go1.5.1/node2]
```
這時2與1兩節點就發現彼此了

4.查看節點所連結的節點
(bootnode)不包含在內
```
./showpeers.sh ethereum-node1
```

5.啟動一個mining節點(挖礦節點)

```
./runminer.sh miner1
```

查看logs

```
docker logs ethereum-miner1
```

挖礦前他會花一些時間準備，所以一開始只會看到如下

```
I0208 08:18:46.744549 ethash.go:291] Generating DAG: 33%
I0208 08:18:53.107984 ethash.go:291] Generating DAG: 34%
```

等到他跑到100%，準備完成後他會開始挖礦，可看如下logs

```
I0208 08:26:48.411384 miner/worker.go:435] 🔨 🔗  Mined 5 blocks back: block #3
I0208 08:26:48.411470 core/database_util.go:353] stored block receipts [989a7a59…]

```
這時可再次查看剛才node1的logs

`docker logs ethereum-node1`
```
I0208 08:27:35.038805 core/database_util.go:303] stored block body [2f9292d4…]
I0208 08:27:35.039183 core/database_util.go:288] stored header #22 [2f9292d4…]
I0208 08:27:35.040653 core/blockchain.go:931] [1486542455040638728] inserted block #22 (0 TXs 0 G 0 UNCs) (2f9292d4...). Took 16.003523ms
I0208 08:27:35.041222 core/blockchain.go:962] imported 1 block(s) (0 queued 0 ignored) including 0 txs in 16.594944ms. #22 [2f9292d4 / 2f9292d4]
```
其他節點將會確認被挖出的block


#下載wallet GUI軟體

https://github.com/ethereum/mist/releases


之後試著用command執行--help，前面需要先指定為wallet程式安裝路徑

ex:
OSX
```
/Applications/Ethereum\ Wallet.app/Contents/MacOS/Ethereum\ Wallet --help

```
windows
```
D:\Ethereum-Wallet\Ethereum-Wallet.exe --help
```

#常見名詞:

Dapp:
>A Dapp (‘decentralized app’) consists of two parts: a frontend, written in HTML, and a backend (think of it as the ‘database’ for your frontend).

geth:

>geth is the the command line interface for running a full ethereum node implemented in Go. 

Ethash:

>以太幣(ether)的挖礦算法叫做Ethash, 又名Dashimoto (Dagger-Hashimoto)，是Hashimoto算法結合Dagger之後產成的一個變種