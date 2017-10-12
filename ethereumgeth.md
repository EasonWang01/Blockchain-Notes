# 簡介

[https://geth.ethereum.org/](https://geth.ethereum.org/)  
ethereum的Go語言實現

# 安裝

[https://geth.ethereum.org/downloads/](https://geth.ethereum.org/downloads/)

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

Arm embeded system

[http://ethembedded.com/?page\_id=171](http://ethembedded.com/?page_id=171)

# 使用

[https://github.com/ethereum/go-ethereum/wiki/geth](https://github.com/ethereum/go-ethereum/wiki/geth)

如果是從source安裝\(或可加入環境變數\)

```
~/go-ethereum/build/bin/geth
```

如果是從安裝檔可直接輸入

```
geth help
```

可用命令  
[https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options](https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options)

#### 1.創建創世區塊\(genesis block\)

custom\_genesis.json

```
{
    "config": {
        "chainId": 123,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
    "difficulty": "0x400",
    "gasLimit": "2100000",
    "alloc": {
    }
}
```

然後輸入

```
geth --datadir "./privatechain" --networkid 123 init custom_genesis.json
```

> 上面將會創建一個privatechain資料夾，裡面存放我們private chain的資訊，genesis blcok會放在/privatechain資料夾/geth/chaindata/...ldb，而account會放在keystore裡面

\(如果是1.6之前版本要改為如下\)

> 如果出現Fatal: invalid genesis file: json: cannot unmarshal hex string of odd length into Go struct field Genesis.extraData of type hexutil.Bytes 為版本&gt;1.6 請參考上面

[https://ethereum.stackexchange.com/questions/15283/getting-error-on-max-invalid-genesis-file-hex-string-has-odd-length](https://ethereum.stackexchange.com/questions/15283/getting-error-on-max-invalid-genesis-file-hex-string-has-odd-length)

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

然後輸入以下，之後開啟Mist wallet就會進入private network

```
geth --datadir "./privatechain" init ./custom_genesis.json   

geth --ipcpath ~/Library/Ethereum/geth.ipc --datadir "./privatechain" --networkid 123
```

> 上面要寫上--ipcpath原因是我們指定了datadir
>
> 以下為各os路徑  後面記得加geth.ipc
>
> Mac
>
> `~/Library/Ethereum/`
>
> **Linux**
>
> `~/.ethereum/`
>
> **Windows**
>
> `~/AppData/Roaming/Ethereum`

方法2

---

也可使用下面產生private network

```
geth init custom_genesis.json 

geth networkid=5
```

這樣打開錢包也可進入private network

> 如果出現錯誤:Fatal: Failed to write genesis block: database already contains an incompatible genesis block
>
> 把路徑下的chaindata資料夾刪除即可    ~/Library/Ethereum/geth/chaindata

---

#### 2.預先分配ether給account

> 先把剛才的節點啟動
>
> ```
> geth --ipcpath ~/Library/Ethereum/geth.ipc --datadir "./privatechain" --networkid 123
> ```

然後打開Mist錢包後先創建帳戶，然後把帳戶號碼複製，之後貼到剛才genesis的alloc下面的一串hash，然後重新init 即可

```
 "alloc": {    "0x66A2e289b35147188876c2007f9a810Dd20e480d": {
                                     "balance": "1337000000000000000000"}
                                      }
```

```
geth --datadir "./privatechain" --networkid 123 init custom_genesis.json
```

> 每次init genesis  chaindata資料夾下都會多一個ldb但他會去讀取最新的
>
> 有時上面訊息有Success 但下面又有Fatal可以不用理會

![](/assets/螢幕快照 2017-10-12 上午5.52.58.png)

之後再次啟動

```
geth --ipcpath ~/Library/Ethereum/geth.ipc --datadir "./privatechain" --networkid 123
```

點選Mist圖案打開錢包即可看到帳戶餘額增加

#### 試著挖礦

打開另一個termianl輸入以下，進入console

```
geth attach
```

> 或是可在剛才啟動ethereum後面加上console字樣，而geth attach預設只會找ipc不會找rpc
>
> 可在mist新增地址或是在console輸入`personal.newAccount("密碼")`  
> 加入挖礦要放錢的地址
>
> ```
> web3.miner.setEtherbase('0xB4eb9148CD0Aa801215f0e62A354F55A7E1AA67A')
> // 上面為剛才新增的地址
> // 也可以直接寫如下 會去讀取剛才創建得地址
> web3.miner.setEtherbase(personal.listAccounts[0])
> ```
>
> 開始挖礦\(需稍等\)
>
> \(目前新版1.6有可能卡在dag產生完後\)
>
> ```
> miner.start(1)
> ```
>
> 停止
>
> ```
> miner.stop()
> ```

#### 3.加入其他節點

[https://github.com/ethereum/go-ethereum/wiki/Setting-up-private-network-or-local-cluster](https://github.com/ethereum/go-ethereum/wiki/Setting-up-private-network-or-local-cluster)

[https://github.com/ethereum/go-ethereum/wiki/Connecting-to-the-network\#connecting-to-the-network](https://github.com/ethereum/go-ethereum/wiki/Connecting-to-the-network#connecting-to-the-network)

1.用RPC，記得要使用`--rpc`，上面那個官網wiki沒寫

2.每個節點要使用同一個創世區塊，才能找到彼此，我們先輸入如下指令，分別創建兩個節點的資料夾，並且用同一個genesis.json個別於兩資料夾產生相同創世區塊

先把剛privatechain資料夾刪除，重新創建一個./privatechain/src 然後裡面放入剛才的custom\_genesis.json

```
sudo rm -rf ./privatechain ; mkdir ./privatechain ; mkdir ./privatechain/src ; mv ./custom_genesis.json ./privatechain/src/custom_genesis.json
```

```
geth --datadir "./privatechain/01" init ./privatechain/src/custom_genesis.json   

geth --datadir "./privatechain/02" init ./privatechain/src/custom_genesis.json
```

3.再來分別啟動兩個節點\(networkid要相同，而`--rpcport`與`--port`要不同\)

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

> 注意  
> 1.  
> 我們沒有設預設bootnode所以要手動加入  
> 2.  
> 如果有三個節點ABC，A加入B之後，A再加入C  
> 這時B仍然找不到C要B主動加入C才可  
> 3.  
> 一開始沒有加入peers時會發現有時輸入admin.peers會出現有時沒有，原因是在連線到任何peers時他會試著去連接其他網路上的節點  
> 4.  
> 節點互相找不到的原因通常為你沒有輸入init genesis.json，所以兩個節點間的genesis.json不相同以及沒有輸入--nodiscover 所以他會加上網路上其他相同genesis和networkid的陌生人  
> 5.  
> 重新啟動某節點該節點的peer會斷掉，要重新加入

7.測試RPC-JSON server

> 之後錢包連線會使用
>
> ```
> 我們可以先輸入 curl http://localhost:8104 
> 8104是該RPC的port，來看一下有沒有啟動
> ```
>
> 成功會出現下圖  
> ![](/assets/螢幕快照 2017-02-10 下午3.27.47.png)

8.這時開啟Mist要設定錢包要連線到哪個位置，所以這次我們不能直接點選圖案開啟，要使用命令列開啟，並加上`--rpc <PORT>`

> \(你可能直接從圖案開啟應用程式，也看到他有開啟時有顯示private net，但其實他還是連到MainNet\)
>
> OSX
>
> ```
> /Applications/Ethereum\ Wallet.app/Contents/MacOS/Ethereum\ Wallet --rpc http://localhost:8104
> ```
>
> Windows
>
> ```
> start <加上程式路徑> --rpc <IP+PORT>
> ```

# \# 1.6新增之puppeth  CLI可用來產生genesis\_block

1.記得先產生account

```
geth --datadir ./privatechain account new
```

2.直接在terminal輸入 \`puppeth\` 之後會出現如下圖

![](/assets/sdc.png)

然後造著步驟走即可，最後會產生一個創世.json

但要記得如果共識方法選的是Poa，之後其他節點要挖礦會產生

```
 Block sealing failed    err=unauthorized
```

記得要

```
clique.propose("帳號", true)
```

clique指令只有POA才有

# 注意事項

1.

> 注意:每次從terminal啟動其他ethereum程序後開啟錢包，如果你沒有指定RPC 或IPC port，錢包都只會去讀取最新開啟的terminal的程序

2.

> 如果進入到錢包，但是testnet與main都無法點選，表示你嘗試進入到private network但是參數沒有下成功

3.

> 每個 `--` 參數都要和前面空一格，否則無效

4

> windows作業系統的terminal如果不好複製，可以使用git bash

5.

> 節點互相找不到的原因通常為你沒有輸入init genesis.json，所以兩個節點間的genesis.json不相同以及沒有輸入--nodiscover 所以他會加上網路上其他相同genesis和networkid的陌生人

1. 兩節點加入後新加入節點不用挖礦就會自動同步，但如果有產生合約或是廣播交易網路上的其中一個節點必須挖礦

![](/assets/螢幕快照 2017-03-25 下午9.16.18.png)

# 使用DEV Chain

> 不用自己init genesis block 和指定datadir 以及自動幫你配好一些參數 可直接啟動私有鏈

```
geth --dev --rpc --rpcport 8104 --rpccorsdomain="*"  console
```

or

```
可加上 --datadir ./test
```

# 開放Remote 連線

[https://ethereum.stackexchange.com/questions/3163/how-can-i-expose-geths-rpc-server-to-external-connections](https://ethereum.stackexchange.com/questions/3163/how-can-i-expose-geths-rpc-server-to-external-connections)

\# 設定Bootnode

\`\`\`

因為homebrew的安裝沒有bootnode executable

所以要build from source

\`\`\`

以為步驟

\`\`\`

git clone [https://github.com/ethereum/go-ethereum.git](https://github.com/ethereum/go-ethereum.git)

make all

cd ./build/bin

./bootnode -genkey bootnode.key

\`\`\`

之後資料夾下會產一個bootnode.key檔案

然後輸入

\`\`\`

./bootnode -nodekey bootnode.key

\`\`\`

會產生如下

![](/assets/螢幕快照 2017-10-12 下午9.17.19.png)  


之後我們開啟terminal輸入以下啟動節點

&gt;

記得--nodiscover要拿掉 然後--bootnodes後面填上剛才出現的enode:

並且把enode\[::\]換成ip地址

bootnode本身不算是節點 只啟動下面一個時在admin.peers還是空的

  


\`\`\`

geth --ipcdisable --rpc --rpcport 8106 --datadir "./privatechain/01" --networkid 123 --rpcapi="db,eth,net,web3,personal" --bootnodes "enode://92d7f28e8ca3bd1e1fa43959bb1cf37b9d284a81d17bcaee9cf233a773f3da0ce60b2351c3b29b07eb78229e6a84731d36065506f6ad972ed3203c5d18bef313@\[127.0.0.1\]:30301" --port=30310 console

  


  


geth --ipcdisable --rpc --rpcport 8107 --datadir "./privatechain/02" --networkid 123 --rpcapi="db,eth,net,web3,personal" --bootnodes "enode://92d7f28e8ca3bd1e1fa43959bb1cf37b9d284a81d17bcaee9cf233a773f3da0ce60b2351c3b29b07eb78229e6a84731d36065506f6ad972ed3203c5d18bef313@\[127.0.0.1\]:30301" --port=30311 console

  


\`\`\`

  


之後在其中一個terminal輸入\`admin.peers\`

即可看到如下

![](/assets/螢幕快照 2017-10-12 下午9.24.16.png)

