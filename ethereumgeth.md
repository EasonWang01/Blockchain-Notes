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


可用命令
https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options

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
>上面要寫上--ipcpath原因是我們指定了datadir



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
>每次init genesis  chaindata資料夾下都會多一個ldb但他會去讀取最新的



之後再次啟動
```
geth --ipcpath ~/Library/Ethereum/geth.ipc --datadir "./privatechain" --networkid 123
```


點選圖案打開錢包即可看到帳戶餘額增加


####試著挖礦


打開另一個termianl輸入以下，進入console

```
geth attach
```
>或是可在剛才啟動ethereum後面加上console字樣，而geth attach預設只會找ipc不會找rpc


>可在mist新增地址或是在console輸入`personal.newAccount("密碼")`
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

https://github.com/ethereum/go-ethereum/wiki/Connecting-to-the-network#connecting-to-the-network

1.用RPC，記得要使用`--rpc`，上面那個官網wiki沒寫

2.每個節點要使用同一個創世區塊，才能找到彼此，我們先輸入如下指令，分別創建兩個節點的資料夾，並且用同一個genesis.json個別於兩資料夾產生相同創世區塊
```
geth --datadir "./privatechain/01" init ./privatechain/src/custom_genesis.json   

geth --datadir "./privatechain/02" init ./privatechain/src/custom_genesis.json  
```
3.再來分別啟動兩個節點(networkid要相同，而`--rpcport`與`--port`要不同)
```
geth  --ipcdisable --rpc --rpcport 8104 --datadir "./privatechain/01" --networkid 123 --rpcapi="db,eth,net,web3,personal" --nodiscover   --port=30310 console

geth  --ipcdisable --rpc --rpcport 8103 --datadir "./privatechain/02" --networkid 123 --rpcapi="db,eth,net,web3,personal" --nodiscover    --port=30308 console     
```
4.之後於01節點的console輸入
```
admin.nodeInfo
```
把`enode`部分複製

![](/assets/螢幕快照 2017-02-10 下午3.20.45.png)

5.於02節點輸入

```
admin.addPeer("貼上剛才複製的enode")
```
6.之後於兩個節點分別輸入`admin.peers`即可找到彼此

![](/assets/螢幕快照 2017-02-10 下午3.24.28.png)

>注意
1.
>我們沒有設預設bootnode所以要手動加入
2.
>如果有三個節點ABC，A加入B之後，A再加入C
這時B仍然找不到C要B主動加入C才可
3.
>一開始沒有加入peers時會發現有時輸入admin.peers會出現有時沒有，原因是在連線到任何peers時他會試著去連接其他網路上的節點
4.
>節點互相找不到的原因通常為你沒有輸入init genesis.json，所以兩個節點間的genesis.json不相同以及沒有輸入--nodiscover 所以他會加上網路上其他相同genesis和networkid的陌生人
5.
>重新啟動某節點該節點的peer會斷掉，要重新加入

7.測試RPC-JSON server

>之後錢包連線會使用
```
我們可以先輸入 curl http://localhost:8104 
8104是該RPC的port，來看一下有沒有啟動
```
成功會出現下圖
![](/assets/螢幕快照 2017-02-10 下午3.27.47.png)




8.這時開啟Mist要設定錢包要連線到哪個位置，所以這次我們不能直接點選圖案開啟，要使用命令列開啟，並加上`--rpc <PORT>`


>OSX
```
/Applications/Ethereum\ Wallet.app/Contents/MacOS/Ethereum\ Wallet --rpc http://localhost:8104
```

>Windows
```
start <加上程式路徑> --rpc <PORT>
```

#注意事項

1.
>注意:每次從terminal啟動其他ethereum程序後開啟錢包，如果你沒有指定RPC 或IPC port，錢包都只會去讀取最新開啟的terminal的程序

2.

>如果進入到錢包，但是testnet與main都無法點選，表示你嘗試進入到private network但是參數沒有下成功

3.

>每個 `--` 參數都要和前面空一格，否則無效

4

>windows作業系統的terminal如果不好複製，可以使用git bash

5.

>節點互相找不到的原因通常為你沒有輸入init genesis.json，所以兩個節點間的genesis.json不相同以及沒有輸入--nodiscover 所以他會加上網路上其他相同genesis和networkid的陌生人
