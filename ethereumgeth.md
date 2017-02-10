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

####1.創建創世區塊(genesis block)

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
geth --datadir "./privatechain" --networkid 123 init custom_genesis.json
```
>上面將會創建一個privatechain資料夾，裡面存放我們private chain的資訊，genesis blcok會放在/privatechain資料夾/geth/chaindata/...ldb，而account會放在keystore裡面

然後輸入以下，之後開啟Mist wallet就會進入private network
```
geth --ipcpath ~/Library/Ethereum/geth.ipc --datadir "./privatechain" --networkid 123


```
>上面要寫上--ipcpath原因是我們指令了datadir



方法2


-----

也可使用下面產生private network

```
geth init custom_genesis.json 

geth networkid=5
```
這樣打開錢包也可進入private network

-----


####2.預先分配ether給account

剛才打開錢包後先創建帳戶，然後把帳戶號碼複製，之後貼到剛才genesis的alloc下面的一串hash，然後重新init 即可
```
 "alloc": {    "0x66A2e289b35147188876c2007f9a810Dd20e480d": {
                                     "balance": "1337000000000000000000"}
                                      }
```


```
geth --datadir "./privatechain" --networkid 123 init custom_genesis.json
```
之後再次啟動，並且打開錢包
```
geth --ipcpath ~/Library/Ethereum/geth.ipc --datadir "./privatechain" --networkid 123
```
>每次init genesis  chaindata資料夾下都會多一個ldb但他會去讀取最新的



####試著挖礦


進入console

>或是可在剛才啟動ethereum後面加上console字樣

```
geth attach
```

加入挖礦要放錢的地址
```
web3.miner.setEtherbase('0xB4eb9148CD0Aa801215f0e62A354F55A7E1AA67A')
```
開始挖礦
```
miner.start()
```


####3.加入其他節點
https://github.com/ethereum/go-ethereum/wiki/Setting-up-private-network-or-local-cluster

因為geth預設會使用ipc進行內部通訊，但我們要多個節點可能會在其他網路，所以我們用RPC，記得要使用`--rpc`，上面那個官網wiki沒寫
```
geth  --ipcdisable --rpc --rpcport 8101 --datadir "./privatechain" --networkid 123 --port=30305
```

再來Mist要設定錢包要連線到哪個位置，所以這次我們不能直接點選圖案開啟，要使用命令列開啟，並加上`--rpc <PORT>`

OSX
```
/Applications/Ethereum\ Wallet.app/Contents/MacOS/Ethereum\ Wallet --rpc http://localhost:8545
```

Windows
```
start <加上程式路徑> --rpc <PORT>
```

#注意事項

1.
>注意:每次從terminal啟動其他ethereum程序後開啟錢包，錢包都只會去讀取最新開啟的terminal的程序

2.

>如果進入到錢包，但是testnet與main都無法點選，表示你嘗試進入到private network但是參數沒有下成功
