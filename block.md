# 區塊鏈運作原理

### 簡介

區塊鏈是一個概念，而比特幣與乙太幣等其他加密貨幣均為區塊鏈的應用之一

以下簡單一句話理解區塊鏈

> 許多交易組成區塊，許多區塊前後互相鏈結，組成區塊鏈

可先看這個簡單介紹影片\
[https://www.youtube.com/watch?v=sYduOfRLHq0](https://www.youtube.com/watch?v=sYduOfRLHq0)

想像今天主管口頭下令新政策，雖然沒有做記錄，但大家聽到後都會互相口耳相傳，之後這個政策雖然沒有被一個中心把它記錄下來，但大家心中都知道這件事情了，如果之後這個下這個政策的主管離職了，剩下的人會以大家多數人講的為算，但如果有人想更改，也會因為與大多數人講的不同而被拒絕

#### 理解區塊鏈

比特幣可以說是目前家喻戶曉的一項使用區塊鏈技術的應用。我們先來說明比特幣是如何運作，說明過程中會將會一步一步講解區塊鏈的相關概念

> Bitcoin is an innovative payment network and a new kind of money. 源自比特幣官網

我們最常聽到比特幣是在新聞報導或朋友閒聊時，談到今天比特幣價格又飆漲到了多少，某位玩家又在中國又投資了新的礦機或礦場本書將會在後續章節介紹如何申請一個自己的比特幣地址與錢包，並且進行購買比特幣與轉帳等動作

大家可能會想的第一個問題可能是: 在電腦上的貨幣他真的夠安全嗎?

接著我們會想到網路上刷信用卡不也是在網路上的虛擬交易嗎，那線上刷信用卡跟我們使用比特幣他的差別又在哪裡

#### 加密貨幣錢包

> 比特幣地址類似於14qViLJfdGaP4EeHnDyJbEGQysnCpwn1g
>
> 可用的比特幣位址數量接近2的161次方個，假如地球上約有2的63次方粒沙，如果每一粒沙中有一個地球，那麼比特幣位址總數遠遠超過所有這些「地球」上的所有的沙子的數量

為了能在區塊鏈網路裡進行交易，你需要一個比特幣地址，它讓你可以存放你的比特幣。比特幣地址是由一個私鑰之後產生公鑰在進行一些密碼學方法出的一個比特幣地址(將會在後續章節詳細介紹產生方式)

每個公鑰都是由一個私鑰所產生

如果一個訊息被公鑰加密，只有配對的私鑰才能解密讀到訊息。反之，如果你用你的私鑰加密訊息，只有配對的公鑰可以解密。所以當小明想要轉帳小王，他需要用他的私鑰將轉帳訊息加密後，送到網路裡，然後每個節點使用小明的公鑰將訊息解開，以確認是由小明發送的。

在加密完成時會產生一個電子簽名，它會被節點們用來確認交易訊息的發送來源和真偽。電子簽名內容是一串文字，它是由私鑰對交易訊息所簽發出的一段hash。如果你更改交易訊息中任何一個字元，電子簽名也會跟著改變，所以駭客很難更改你的交易訊息或是得知交易金額。

![](.gitbook/assets/s.png)

**查詢餘額**

我們轉帳後比特幣是如何知道我們現在帳戶餘額是多少呢?

剛才有提到比特幣網路的組成是由全世界跑著比特幣節點程式的電腦所構成

每個節點電腦會進行驗證交易與同步其他節點資料的動作且都保有一份區塊鏈帳本，但節點是如何知道你的帳戶餘額？區塊鏈系統並沒有記錄每個人的帳戶餘額，事實上，它只有紀錄網路上每筆交易紀錄

> _持有比特幣代表的是，於區塊鏈帳本上查詢你的地址還存在著尚未變成輸入交易之紀錄。_

![](.gitbook/assets/SAA.png)看到上圖

如果在out沒有連接著其他in的話則其即變為目前帳戶的餘額

#### 區塊中的交易

![](.gitbook/assets/SA.png)看到上圖,每個產生的交易會先在交易池中等待被節點納入區塊中

每個節點都可以將若干個交易訊息從交易池中選取並打包成區塊發送到網絡上，並建議其為鏈上的最新區塊。

區塊鏈系統使用密碼學方法(後面章節會提到實作)設計了一道複雜的數學題，只要有正確答案，就可以成爲鏈上的最新區塊。這個答案是由節點計算後一起被打包進區塊。答案是一個數字，得到答案的唯一方法就是不斷地計算。哪個節點先得到了答案，它就先有權利將它的候選區塊放到鏈上。

每個節點都有一份區塊鏈副本，當節點拿到其他節點丟出來的候選區塊(已有正確答案的)，就會把區塊放到副本鏈上，同時系統會要求節點搜尋網路裡有沒有比它的副本鏈更長的鏈，如果有，捨棄原有的鏈，取用更長的鏈。

![](<.gitbook/assets/螢幕快照 2017-02-07 下午3.09.29.png>)

在上圖中，我們可以看到每個方塊中都包含一些數值 這些值即為存放在區塊鏈中的區塊資料

1.區塊鏈是由包含交易訊息的區塊，從後向前有序接起來的數據結構，區塊被從後向前有序地鏈接在這個鏈條裡，每個區塊都指向前一個區塊

2.對每個區塊頭進行SHA256加密哈希，每個區塊頭都包含它的父區塊哈希值。這樣把每個區塊鏈接到各自父區塊的哈希值序列就創建了一條一直可以追溯到第一個區塊（創世區塊）的鏈條

3.由於區塊頭包含“父區塊哈希值”，所以當前區塊的哈希值因此也受到該字段的影響。如果父區塊的hash不一樣，則子區塊也會有不同的hash，接著這個子區塊的子區塊也會有不同hash，這種瀑布效應將保證該區塊不會被改變

4.區塊內包含以下，區塊頭80字節(byte)，而平均每個交易至少是250字節，而且平均每個區塊至少包含超過500個交易

另一個相關介紹影片\
[https://www.youtube.com/watch?v=r43LhSUUGTQ](https://www.youtube.com/watch?v=r43LhSUUGTQ)

## Merkle Tree

如果今天在區塊中的某一筆交易本文被竄改，則計算出來之Merkel Root也一定不同

利用這個特性可避免區塊中所含的歷史交易遭任意竄改

![](<.gitbook/assets/螢幕快照 2017-02-07 下午5.03.21.png>)

Merkle樹被用來歸納一個區塊中的所有交易，同時生成整個交易集合的數字指紋，且提供了一種校驗區塊是否存在某交易的高效途徑

為了創建父節點HAB，子節點A和子節點B的兩個32字節的哈希值將被串聯成64字節的字符串。隨後將字符串進行兩次哈希來產生父節點的哈希值:

```
HAB~=SHA256(SHA256(HA + HB))
```

## 挖礦

> Mining = Securing the network = verify computation

一個新的區塊被“挖掘”出來，每個區塊裡包含著從上一個區塊產生到目前這段時間內發生的所有交易，這些交易被依次添加到區塊鏈中。我們把包含在區塊內且被添加到區塊鏈上的交易稱為“確認”交易，交易經過“確認”之後，新的擁有者才能夠花費他在交易中得到的比特幣

礦工們在挖礦過程中會得到兩種類型的獎勵：創建新區塊的新幣獎勵，以及區塊中所含交易的交易費

在挖礦過程中成功“挖出”新區塊的礦工可以得到該區塊中包含的所有交易“小費”。目前，這筆費用佔礦工收入的0.5%或更少，大部分收益仍來自挖礦所得的比特幣獎勵

> Finding a nonce input to the algorithm so that the result is below a certain threshold

區塊鏈用難度來控制下一個區塊的產生速度，而礦工利用當前的區塊頭加上一個隨機數nonce，並做hash，如果產生一個低於設定難度的值即可成功挖到礦

藉由改變nonce來產生不同hash，想辦法讓此hash小於目前所設定難度的數值

[https://www.youtube.com/watch?v=lik9aaFIsl4\&t=1s](https://www.youtube.com/watch?v=lik9aaFIsl4\&t=1s)

挖礦困難度解釋

[https://en.bitcoin.it/wiki/Target](https://en.bitcoin.it/wiki/Target)

挖礦時間估計

[http://coinish.com/bitcoin-mining-calculator/#calc-information](http://coinish.com/bitcoin-mining-calculator/#calc-information)

#### 我們可以使用以下網頁進行挖礦模擬

[https://anders.com/blockchain/block.html](https://anders.com/blockchain/block.html)

> ![](.gitbook/assets/02.png)Nonce為一個隨機值讓我們更改 實際挖礦時即為更改Nonce並想辦法算出結果 因為相同字串經過sha後的數值都一樣
>
> 但無法預料哪個字串經過sha可以得到我們想要的數值
>
> Data為模擬納入區塊的訊息

## 看到目前市面上的幣種以及價格

當我們想要對目前市面上的加密貨幣進行投資時，我們必須先到市場上了解其價值

以下網站列出了一些市面上的加密貨幣可供我們參考

[https://coinmarketcap.com/](https://coinmarketcap.com/)

## 其他相關Bitcoin schema介紹

以下網站列出了比特幣相關schema

[https://webbtc.com/api/schema](https://webbtc.com/api/schema)

![](.gitbook/assets/04.png)

## 加密貨幣Market State圖表

一樣列了許多市面上可見的加密貨幣，但採用了另一種方式將之呈現

[http://cryptomaps.org/](http://cryptomaps.org/)

![](.gitbook/assets/03.png)
