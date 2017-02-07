![](/assets/螢幕快照 2017-02-07 下午3.09.29.png)


1.區塊鏈是由包含交易訊息的區塊，從後向前有序接起來的數據結構，區塊被從後向前有序地鏈接在這個鏈條裡，每個區塊都指向前一個區塊

2.對每個區塊頭進行SHA256加密哈希，每個區塊頭都包含它的父區塊哈希值。這樣把每個區塊鏈接到各自父區塊的哈希值序列就創建了一條一直可以追溯到第一個區塊（創世區塊）的鏈條

3.由於區塊頭包含“父區塊哈希值”，所以當前區塊的哈希值因此也受到該字段的影響。如果父區塊的hash不一樣，則子區塊也會有不同的hash，接著這個子區塊的子區塊也會有不同hash，這種瀑布效應將保證該區塊不會被改變

4.區塊內包含以下，區塊頭80字節(byte)，而平均每個交易至少是250字節，而且平均每個區塊至少包含超過500個交易

5.你可以下載https://bitcoin.org/en/full-node，讓你電腦執行程式後成為一個full node

full node 意思是讓你電腦成為一個節點，擁有比特幣的開始到現在的所有交易訊息，並且你會需要幫忙認證區塊的正確性

全世界的full node https://bitnodes.21.co/



```
wget https://bitcoin.org/bin/bitcoin-core-0.13.2/bitcoin-0.13.2-x86_64-linux-gnu.tar.gz

tar -xvzf bitcoin-0.13.2-x86_64-linux-gnu.tar.gz 

cd bitcoin-0.13.2/bin
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