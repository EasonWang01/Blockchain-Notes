#簡介

https://geth.ethereum.org/
ethereum的Go語言實現

#安裝

https://geth.ethereum.org/downloads/
```
git clone https://github.com/ethereum/go-ethereum

sudo apt-get install -y build-essential golang

cd go-ethereum

make geth
```

Mac

```
brew tap ethereum/ethereum

brew install ethereum
```

#使用

https://github.com/ethereum/go-ethereum/wiki/geth

如果是從source安裝(或可加入環境變數)
```
~/go-ethereum/build/bin/geth
```

如果是從安裝檔可直接輸入
```
geth help
```

####創建創世區塊(genesis block)

genesis.json
```
{
    "nonce": "0x0000000000000042",
    "timestamp": "0x0",
    "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "extraData": "0x0",
    "gasLimit": "0x8000000",
    "difficulty": "0x400",
    "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "coinbase": "0x3333333333333333333333333333333333333333",
    "alloc": {
    }
}
```
然後輸入
```
geth init genesis.json
```
來啟動bootnode

```
geth networkid=5 console

```



####Command option
https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options

