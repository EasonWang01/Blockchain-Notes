#簡介:
以太坊（英語：Ethereum）是一個開源且具有智慧合約區塊鏈平台，設計上是為了解決比特幣擴展性不足的問題

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


#常見名詞:

Dapp:
>A Dapp (‘decentralized app’) consists of two parts: a frontend, written in HTML, and a backend (think of it as the ‘database’ for your frontend).


