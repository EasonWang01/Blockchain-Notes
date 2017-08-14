[https://github.com/ethereum/go-ethereum/wiki/Running-in-Docker](https://github.com/ethereum/go-ethereum/wiki/Running-in-Docker)

# 安裝:

這邊我們會使用`Docker`，並使用ethereum相關的container來進行   
[https://hub.docker.com/r/ethereum/client-go/](https://hub.docker.com/r/ethereum/client-go/)

所以須先安裝好`Docker`[https://docs.docker.com/engine/installation/](https://docs.docker.com/engine/installation/)  
並且執行

再來開啟terminal

1.加入以太坊的public network

```
docker run -d --name ethereum-PublicNode \
    -v $HOME/.ethereum:/root \
    -p 8545:8545 -p 30303:30303 \
    ethereum/client-go --fast --cache=512
```

執行attach

```
docker exec -ti ethereum-PublicNode geth attach
```

2.加入以太坊的test network

```
docker run -d --name ethereum-TestNode \
    -v $HOME/.ethereum:/root \
    -p 8545:8545 -p 30303:30303 \
    ethereum/client-go --testnet --fast --cache=512
```

執行attach

```
docker exec -ti ethereum-TestNode \
    geth attach ipc:/root/.ethereum/testnet/geth.ipc
```

查看LOG

```
docker logs ethereum-Testnode
```

> attach的意思是在terminal啟動一個javascript的相關以太坊API執行環境  
> [https://github.com/ethereum/wiki/wiki/JavaScript-API](https://github.com/ethereum/wiki/wiki/JavaScript-API)

但我們這邊主要教學部分為Private chain  
[https://github.com/vertigobr/ethereum](https://github.com/vertigobr/ethereum)

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
docker run .... -e GEN_NONCE='stag0x2222ing'
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

## 1.啟動起始節點

```
./bootnode.sh
```

使用`docker logs ethereum-bootnode`查看log

查看剛才的boot節點

```
./getbootnodeurl.sh
```

查看log

```
docker logs ethereum-bootnode
```

## 2.再來啟動另一個非mining的節點

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

## 3.新增第二個非mining的節點

```
./runnode.sh node2
```

這時我們回去察看node1的log，可看到類似如下

```
Peer e4a6e56cf9096420 [eth/63]: peer connected [Geth/v1.4.11-stable/linux/go1.5.1/node2]
```

這時2與1兩節點就發現彼此了

## 4.查看節點所連結的節點

\(bootnode\)不包含在內

```
./showpeers.sh ethereum-node1
```

## 5.啟動一個mining節點\(挖礦節點\)

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

#### 下載wallet GUI軟體

[https://github.com/ethereum/mist/releases](https://github.com/ethereum/mist/releases)

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

> ps:有時直接cd 到他的目錄下下指令會無法執行

## 6.使用Mist讀取private network

1.  
開啟RPC server

```
RPC_PORT=8545 ./runminer.sh wallet
```

2.使用錢包讀取該RPC server

```
/Applications/Ethereum\ Wallet.app/Contents/MacOS/Ethereum\ Wallet --rpc http://localhost:8545
```

## 7.從Mist新建一個account，並且綁定帳號，之後開始挖礦

從錢包複製新建帳號好把他與礦工節點綁定

ETHERBASE後面為你剛才複製的帳號

```
ETHERBASE=0xC21026c0026D47B76B5e3249b981b73E6f734212 RPC_PORT=8545 ./runminer.sh wallet
```

查看log

```
docker logs ethereum-wallet
```

然後等待DAG完成後會開始挖礦

![](/assets/螢幕快照 2017-02-10 下午4.52.15.png)

之後重新啟動錢包，即可看到ether增加

## 8.轉帳

我們再從Mist中新增一個帳號，然後點選該新帳號，點選右側的`Transfer Ether`

輸入轉出金額=&gt;調整手續費=&gt;確認密碼=&gt;轉帳成功＝&gt;需要稍等才會進入帳戶

