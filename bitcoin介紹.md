

你可以下載 https://bitcoin.org/en/full-node ，讓你電腦執行程式後成為一個full node

full node 意思是讓你電腦成為一個節點，擁有比特幣的開始到現在的所有交易訊息，並且你會需要幫忙認證區塊的正確性

全世界的full node https://bitnodes.21.co/


上面的安裝需要至少90GB



所以我們可以直接使用編譯好的且不包含一些其他例如DB等檔案在內的壓縮檔

```
https://bitcoin.org/bin/bitcoin-core-0.13.2/
```

啟動server
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

有關cli command的所有指令

https://en.bitcoin.it/wiki/Original_Bitcoin_client/API_calls_list


# #有關手續費

```
輸出（outputs）小於0.01BTC（包括你錢包內部的資金變動）的話，你必須要支付0.0001的手續費，即使是你自己轉給你自己
```

#交易

每筆交易均須付手續費，最少為`0.00001BTC`付越多交易越快被確認



#Satoshi

```
0.00000001 bitcoin 為一個 satoshi，此也為bitcoin的最小單位
```

