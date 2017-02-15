
我們一樣先執行剛才的節點

```
geth  --ipcdisable --rpc --rpcport 8104 --datadir "./privatechain/01" --networkid 123 --rpcapi="db,eth,net,web3,personal" --port=30310 console
```





下面是我們這次要加入的token合約


```
contract token { 
    mapping (address => uint) public coinBalanceOf;
    event CoinTransfer(address sender, address receiver, uint amount);

  /* Initializes contract with initial supply tokens to the creator of the contract */
  function token(uint supply) {
        coinBalanceOf[msg.sender] = supply;
    }

  /* Very simple trade function */
    function sendCoin(address receiver, uint amount) returns(bool sufficient) {
        if (coinBalanceOf[msg.sender] < amount) return false;
        coinBalanceOf[msg.sender] -= amount;
        coinBalanceOf[receiver] += amount;
        CoinTransfer(msg.sender, receiver, amount);
        return true;
    }
}
```


```
var tokenSource = ' contract token { mapping (address => uint) public coinBalanceOf; event CoinTransfer(address sender, address receiver, uint amount); /* Initializes contract with initial supply tokens to the creator of the contract */ function token(uint supply) { coinBalanceOf[msg.sender] = supply; } /* Very simple trade function */ function sendCoin(address receiver, uint amount) returns(bool sufficient) { if (coinBalanceOf[msg.sender] < amount) return false; coinBalanceOf[msg.sender] -= amount; coinBalanceOf[receiver] += amount; CoinTransfer(msg.sender, receiver, amount); return true; } }'

```


把上面的部分複製到我們的geth console 中

然後進行編譯

```
var tokenCompiled= web3.eth.compile.solidity(tokenSource)
```

之後試著輸入以下，即可看到剛才compile後的部分

```

tokenCompiled["<stdin>:token"].info.abiDefinition //查看我們合約的API
```


然後我們先用以下指令，確定我們鏈上有帳號(也可查看keystore資料夾)
```
web3.eth.accounts
```
如果沒有可打開Mist新增

```
/Applications/Ethereum\ Wallet.app/Contents/MacOS/Ethereum\ Wallet --rpc http://localhost:8104
```
之後輸入以下綁定帳號到節點
```
web3.miner.setEtherbase("輸入地址")
```

然後輸入以下來解鎖帳號(讓帳號可以交易)

```
personal.unlockAccount("address", "password")
```


接著把我們剛才的合約實例化，並部署

```
var supply = 10000;
var tokenContract = web3.eth.contract(tokenCompiled["<stdin>:token"].info.abiDefinition);
var token = tokenContract.new(
  supply,
  {
    from:web3.eth.accounts[0], 
    data:tokenCompiled["<stdin>:token"].code, 
    gas: 1000000
  }, function(e, contract){
      if(e){console.log(e); }
    if(!e) {
      if(!contract.address) {
        console.log("Contract transaction send: TransactionHash: " + contract.transactionHash + " waiting to be mined...");

      } else {
        console.log("Contract mined! Address: " + contract.address);
        console.log(contract);
      }

    }
})
```



正常的話會出現如下
```
I0215 20:34:36.263099 internal/ethapi/api.go:1074] Tx(0x09d25084b1d471a48c371bb98f54dde88b59af24348a7b2b29a09bc91c7c9727) created: 0x14067c5707025c4dabba49949c0c166070b4f5c9
Contract transaction send: TransactionHash: 0x09d25084b1d471a48c371bb98f54dde88b59af24348a7b2b29a09bc91c7c9727 waiting to be mined...

```


再來為了要把合約加入Blockchain我們要用挖礦方式產生新區塊

因為在私鏈所以我們要自己挖

```
miner.start(1)
```

產生完Dag後接著挖到contract後可以stop

![](/assets/螢幕快照 2017-02-13 下午4.01.53.png)

```
miner.stop()
```
然後就可以輸入以下

```
token.coinBalanceOf(web3.eth.accounts[0])
```
即會出現餘額


##2.把剛才的合約部署到其他節點

為了使得其他人可以運行你的智能合約，你需要兩個資訊：
1.智能合約地址Address
2.智能合約ABI（Application Binary Interface），ABI其實就是一個有序的用戶手冊，描述了所有方法的名字和如何調用它們。

我們可以使用如下獲得其ABI和智能合約地址:


```
greeterCompiled["<stdin>:greeter"].info.abiDefinition
```

```
greeter.address
```

接著我們到另一個節點的console輸入如下，把ABI與Address更改為剛讀取出來的值(建議開一個檔案修改，之後再貼到console，因為code多console不好修改)

```javascript
var greeter = eth.contract(ABI).at(Address);
```

再來於另外一個terminal輸入

```
greeter.greet()
```

如果出現下圖錯誤，我們可以跟另一個節點做區塊鏈同步即可解決

![](/assets/螢幕快照 2017-02-13 下午4.23.53.png)

所以先把第一個節點加入，步驟如下

於第一個節點輸入`admin.nodeInfo` => 複製enode url=> 第二個節點輸入`admin.addPeer("剛才複製的enode url")`

之後再到第二個節點輸入以下，即可同步區塊
```
miner.start(1)
```

此時第二個節點也會出現Hello World了!