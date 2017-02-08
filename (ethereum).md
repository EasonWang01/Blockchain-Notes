##Dapp

>A Dapp (‘decentralized app’) consists of two parts: a frontend, written in HTML, and a backend (think of it as the ‘database’ for your frontend).




#安裝環境

相關GUI介紹

http://ethereum.stackexchange.com/questions/8/what-are-the-different-guis-available-for-ethereum


這邊我們會用`Docker`來執行ethereum

###安裝Docker

簡介:
想像是一個虛擬主機，上面設定好了環境，每次我們下`Docker run`
指令會去Docker的線上倉庫下載image之後執行，省去我們設定環境的時間


安裝網址:
https://www.docker.com/products/overview

安裝後執行`Docker`


開始使用ethereum的go client的image

1.打開terminal輸入以下來加入public network

```
docker run -d --name ethereum-node \
    -v $HOME/.ethereum:/root \
    -p 8545:8545 -p 30303:30303 \
    ethereum/client-go --fast --cache=512
```

接著即可執行attach
```
docker exec -ti ethereum-node geth attach
```

2.或是加入test network

```
docker run -d --name ethereum-node \
    -v $HOME/.ethereum:/root \
    -p 8545:8545 -p 30303:30303 \
    ethereum/client-go --testnet --fast --cache=512
```

接著即可執行attach
```
docker exec -ti ethereum-node \
    geth attach ipc:/root/.ethereum/testnet/geth.ipc
```

接著即可執行attach


>Attach意思是執行JavaScript Console，接著即可在terminal上使用web3.js library

https://github.com/ethereum/go-ethereum/wiki/JavaScript-Console
