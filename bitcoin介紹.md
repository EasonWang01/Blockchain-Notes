# \#以下三個為線上生成比特幣地址的網站

> 產生主鏈地址網站
>
> [https://www.bitaddress.org/bitaddress.org-v1.9-SHA1-a487b495d710d6f617d688e5f758e40c8b6c510e.html?mainnet=true](https://www.bitaddress.org/bitaddress.org-v1.9-SHA1-a487b495d710d6f617d688e5f758e40c8b6c510e.html?mainnet=true)
>
> 產生測試鏈testnet地址網站
>
> [https://www.bitaddress.org/bitaddress.org-v1.9-SHA1-a487b495d710d6f617d688e5f758e40c8b6c510e.html?testnet=true](https://www.bitaddress.org/bitaddress.org-v1.9-SHA1-a487b495d710d6f617d688e5f758e40c8b6c510e.html?testnet=true)
>
> 取得測試比特幣[https://testnet.manu.backend.hamburg/faucet](https://testnet.manu.backend.hamburg/faucet)

# \#比特幣相關交易訊息與圖表

可以在以下網站看到目前的相關比特幣交易資訊

[https://blockchain.info/zh-cn/](https://blockchain.info/zh-cn/)

比特幣歷史訊息圖表

[https://blockchain.info/charts](https://blockchain.info/charts)

比特幣相關狀態

[http://statoshi.info/](http://statoshi.info/)

# \#安裝比特幣節點

你可以下載 [https://bitcoin.org/en/full-node](https://bitcoin.org/en/full-node) ，讓你電腦執行程式後成為一個full node

full node 意思是讓你電腦成為一個節點，擁有比特幣的開始到現在的所有交易訊息，並且你會需要幫忙認證區塊的正確性

全世界的full node [https://bitnodes.21.co/](https://bitnodes.21.co/)

上面的安裝需要至少90GB

[https://bitcoin.org/bin/bitcoin-core-0.13.2/](https://bitcoin.org/bin/bitcoin-core-0.13.2/)

所以我們可以直接使用編譯好的且不包含一些其他例如DB等檔案在內的壓縮檔

```
在剛才網址內選擇自己平台的版本安裝
// 如果是下載window exe版本 bitcoind 跟bitcoin-cli會在C:\Program Files\Bitcoin\daemon
//也可直接開啟bitcoin-qt圖形介面然後點選上方的說明->除錯視窗->主控台 也可輸入指令
```

之後開啟terminal進入該解壓後的資料夾即可

![](/assets/螢幕快照 2017-02-16 下午2.17.24.png)

啟動server\(&意思是讓他在背景程序執行\)

```
./bitcoind &
```

```
./bitcoin-cli getinfo
```

停止server

```
./bitcoin-cli stop
```

開啟server後他會開始做sync同步其他區塊，所以電腦會開始佔用儲存空間，之後可以把它整個bitcoin資料夾刪除，因為我們這裡只是要測試他的基本指令

Bitcoin 資料夾儲存位置

OSX

```
~/Library/Application\ Support/Bitcoin/
```

有關cli command的所有指令

[https://en.bitcoin.it/wiki/Original\_Bitcoin\_client/API\_calls\_list](https://en.bitcoin.it/wiki/Original_Bitcoin_client/API_calls_list)

產生一個比特幣錢包地址

```
./bitcoin-cli getnewaddress
```

接著取得私鑰

```
./bitcoin-cli dumpprivkey <輸入剛才的地址>
```

> 因為地址跟私鑰都是在我們電腦產生才可以這樣去取得，不然通常是要用私鑰來產生地址

查看地址內的餘額

```
./bitcoin-cli getreceivedbyaddress 1KixkqDyrDkwoEnYbwiha4D5oyjzVbrQLB
```

getrawtransaction把交易哈希值（txid）當做一個參數，並且把整個交易以一個十六進位字串的形式返回，而這也是交易在比特幣網路上存在的形式：

```
./bitcoin-cli getrawtransaction <txid>
```

解碼這個十六進製字符串，我們使用decoderawtransaction命令

```
./bitcoin-cli decoderawtransaction <剛才得到的字串>
```

# 執行testenet

可以用bitcoin-qt 或 bitcoind 執行testnet

```
<...Path>/bitcoin-qt.exe -testnet -datadir="可指定要存放鏈資料的資料夾"
```

執行後如下圖  
![](/assets/螢幕快照 2017-05-05 上午10.59.04.png)

可到以下網站取得testnet的免費bitcoin  
[https://testnet.manu.backend.hamburg/faucet](https://testnet.manu.backend.hamburg/faucet)

# \#有關手續費

[https://bitcoinfees.earn.com/](https://bitcoinfees.earn.com/)  \(此網站可看到目前推薦的手續費與尚未確認的交易所含的手續費\)

> 注意:他是以bytes為單位

# \#Satoshi

```
0.00000001 bitcoin 為一個 satoshi，此也為bitcoin的最小單位
```

# \#區塊高度

區塊在區塊鏈裡的位置，區塊高度也可作為元數據存儲在一個索引數據庫表中以便快速檢索

# \#第一次commit

[https://github.com/bitcoin/bitcoin/commit/4405b78d6059e536c36974088a8ed4d9f0f29898](https://github.com/bitcoin/bitcoin/commit/4405b78d6059e536c36974088a8ed4d9f0f29898)

