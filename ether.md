#簡介:


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


