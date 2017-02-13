#智能合約

#Solidity

安裝


此方法速度較快，其他需花比較長時間
```
git clone --recursive https://github.com/ethereum/solidity.git
cd solidity
```
安裝完可在terminal輸入solc試試看

之後我們一樣先執行剛才的節點

```
geth  --ipcdisable --rpc --rpcport 8104 --datadir "./privatechain/01" --networkid 123 --port=30310 console
```

然後加入我們的第一個合約

(網頁版Code)
https://yeasy.gitbooks.io/blockchain_guide/content/ethereum/smartContract_example01.sol
```
var greeterSource = 'contract mortal { address owner; function mortal() { owner = msg.sender; } function kill() { if (msg.sender == owner) selfdestruct(owner); } } contract greeter is mortal { string greeting; function greeter(string _greeting) public { greeting = _greeting; } function greet() constant returns (string) { return greeting; } }'
```

把上面的部分複製到我們的geth console 中

然後進行編譯

```
var greeterCompiled = web3.eth.compile.solidity(greeterSource)
```

之後試著輸入以下，即可看到剛才compile後的部分

```
greeterCompiled["<stdin>:greeter"]
```

