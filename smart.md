# 智能合約

用來執行我們想要的客製化操作，目前多數使用`Solidity`語言撰寫，每個合約compile後都有一個地址，並且有一個schema，之後每個節點必須主動放入該份擁有合約地址與schema的文件，才能呼叫該合約

---

# Solidity

## 1.第ㄧ部分:安裝

此方法速度較快，其他需花比較長時間

```
git clone --recursive https://github.com/ethereum/solidity.git
cd solidity
```

安裝完可在terminal輸入solc試試看

之後我們一樣先執行剛才的節點

```
geth  --ipcdisable --rpc --rpcport 8104 --datadir "./privatechain/01" --networkid 123 --rpcapi="db,eth,net,web3,personal" --port=30310 console
```

然後加入我們的第一個合約

```
var greeterSource = 'contract mortal { address owner; function mortal() { owner = msg.sender; } function kill() { if (msg.sender == owner) selfdestruct(owner); } } contract greeter is mortal { string greeting; function greeter(string _greeting) public { greeting = _greeting; } function greet() constant returns (string) { return greeting; } }'
```

# 如果版本大於1.6建議直接使用online compiler\(Remix Editor\)

\(因為如果版本大於1.6之後web3.eth.compile.solidity會出現The method eth\_compileSolidity does not exist/is not available錯誤\)

> 官方本身的tutorial還沒更新

online compiler網址:  [https://ethereum.github.io/browser-solidity/\#version=soljson-v0.4.11+commit.68ef5810.js](https://ethereum.github.io/browser-solidity/#version=soljson-v0.4.11+commit.68ef5810.js)

然後直接貼上 以下

```
pragma solidity ^0.4.0;
contract SimpleStorage {
  uint storedData;

  function set(uint x) {
      storedData = x;
  }

  function get() constant returns (uint) {
      return storedData;
  }
}
```

如果還沒有帳號先輸入personal.newAccount\(\) 新增    否則直接貼上部署的code會出現   &gt; Error: authentication needed: password or unlock undefined

之後輸入以下綁定帳號到節點

```
web3.miner.setEtherbase(web3.eth.accounts[0])
```

然後輸入以下來解鎖帳號\(讓帳號可以交易\)

```
personal.unlockAccount(web3.eth.accounts[0], "password")
```

然後先挖礦產生一些ether才能部署合約

```
miner.start(1)
```

之後點選網頁右側的Details 把下圖右下方web3 deploy 的code到剛才執行terminal的web3 console中

![](/assets/螢幕快照 2017-06-11 上午9.44.22.png)

接著回到網頁點選右側最下方的 \(contract detail\) 把interface的部分複製到我們的geth console 中

然後挖出contract\(第一次要先等他跑Dag\)

```
miner.start(1)
```

試著輸入

```
browser_ballot_sol_simplestorage.set('123')
```

## PS:如果出現\`invalid address\`要先輸入以下

```
web3.eth.defaultAccount = web3.eth.accounts[0]
```

# 然後輸入

```
 browser_ballot_sol_simplestorage.set('123')
```

# 之後挖礦

```
miner.start(1)
```

# 然後即可看到剛才新設定的值

```
browser_ballot_sol_simplestorage.get()
```

# 

# 1.5.8版本之前適用以下

```
var greeterCompiled = web3.eth.compile.solidity(greeterSource)
```

之後試著輸入以下，即可看到剛才compile後的部分

```
greeterCompiled["<stdin>:greeter"]

greeterCompiled["<stdin>:greeter"].code   //編譯好的機器碼

greeterCompiled["<stdin>:greeter"].info.abiDefinition //查看我們合約的API
```

以下為編譯好的合約內容

```
[{
    constant: false,
    inputs: [],
    name: "kill",
    outputs: [],
    payable: false,
    type: "function"
}, {
    constant: true,
    inputs: [],
    name: "greet",
    outputs: [{
        name: "",
        type: "string"
    }],
    payable: false,
    type: "function"
}, {
    inputs: [{
        name: "_greeting",
        type: "string"
    }],
    payable: false,
    type: "constructor"
}]
```

我們剛才程式碼中的`_greeting`還沒定義所以輸入以下

```
var _greeting = "Hello World!"
```

接著把我們剛才的合約實例化

```
var greeterContract = web3.eth.contract(greeterCompiled["<stdin>:greeter"].info.abiDefinition);
```

然後我們先用以下指令，確定我們鏈上有帳號\(也可查看keystore資料夾\)

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

然後輸入以下來解鎖帳號\(讓帳號可以交易\)

```
personal.unlockAccount("address", "password")
```

接著是部署

> 如果說餘額不夠可先`miner.start(1)`，確認餘額eth.getBalance\(eth.coinbase\)

```
var greeter = greeterContract.new(_greeting,{from:web3.eth.accounts[0], data: greeterCompiled["<stdin>:greeter"].code, gas: 300000}, function(e, contract){
if(e) { console.log(e) };
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
Contract transaction send: TransactionHash: 0xd913a9fef18e0464b99c2db1d4e847d92647a6dc1054aef310e3104914a6440a waiting to be mined...
```

# 第二部分

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

然後就可以輸入以下，如果出現`Hello world`就成功了

```
greeter.greet();
```

## 2.把剛才的合約部署到其他節點

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

接著我們到另一個節點的console輸入如下，把ABI與Address更改為剛讀取出來的值\(建議開一個檔案修改，之後再貼到console，因為code多console不好修改\)

```javascript
var greeter = eth.contract(ABI).at(Address);
```

> ex:
>
> ```
> var greeter = eth.contract([{
>     constant: false,
>     inputs: [],
>     name: "kill",
>     outputs: [],
>     payable: false,
>     type: "function"
> }, {
>     constant: true,
>     inputs: [],
>     name: "greet",
>     outputs: [{
>         name: "",
>         type: "string"
>     }],
>     payable: false,
>     type: "function"
> }, {
>     inputs: [{
>         name: "_greeting",
>         type: "string"
>     }],
>     payable: false,
>     type: "constructor"
> }]).at("0xe3c599ac956bc790d7b7c140073004dc1ea03c2a");
> ```

再來於另外一個terminal輸入

```
greeter.greet()
```

如果出現下圖錯誤，我們可以跟另一個節點做區塊鏈同步即可解決

![](/assets/螢幕快照 2017-02-13 下午4.23.53.png)

所以先把第一個節點加入，步驟如下

於第一個節點輸入`admin.nodeInfo` =&gt; 複製enode url=&gt; 第二個節點輸入`admin.addPeer("剛才複製的enode url")`

之後再到第二個節點輸入以下，即可同步區塊

```
miner.start(1)
```

此時第二個節點也會出現Hello World了!

線上編譯合約：[https://ethereum.github.io/browser-solidity/\#version=soljson-v0.4.9+commit.364da425.js](https://ethereum.github.io/browser-solidity/#version=soljson-v0.4.9+commit.364da425.js)

> 如果出現錯誤如下，記得指定solidity版本
>
> ```
> Untitled:2:1: Warning: Source file does not specify required compiler version!Consider adding "pragma solidity ^0.4.9
> contract HelloWorld {
> ^
> Spanning multiple lines.
> ```

```
   pragma solidity ^0.4.8;

contract HelloWorld {

    uint public balance;

    function HelloWorld() {
        balance = 1000;
    }
}
```

```
pragma solidity ^0.4.0;

contract bSimpleStorage {
    uint storedData;
    struct Patient {
        string name;
        uint weight; // weight is accumulated by delegation
        uint height;  // if true, that person already voted
        uint NHSNum;  //include timestamp
        bytes32 specialty;  
        bytes32 consultant;
        bytes32 diagnoses;
        uint Creatinite;
        uint Sodium;
        uint Potassium;
        uint eGFR;
        uint Urea;
    }
    struct tests {
        string name ;
    }

    tests[] testss; 
    function x() {
         testss.push(tests({
            name: "123"
        }));
    }

    Patient[] patients;


    uint16 [] aa = [1,2];
    //新增病人
    function addPatient(string name, uint weight, uint height, uint NHSNum) {
        patients.push(Patient({
            name: name,
            weight: weight,
            height: height,
            NHSNum: NHSNum, 
            specialty: '',  
            consultant: '',
            diagnoses: '',
            Creatinite: 0,
            Sodium: 0,
            Potassium: 0,
            eGFR: 0,
            Urea: 0
        }));
    }


    //更新肌酸酐
    function updateCreatinite(string name, uint num) {
        for (uint p = 0; p < patients.length; p++) {
            if (sha3(patients[p].name) == sha3(name)) {
               patients[p].Creatinite = num;
            }
        }
    }
    //讀取
    function getCreatinite(string name) returns(uint Creatinite) {
        for (uint p = 0; p < patients.length; p++) {
            if (sha3(patients[p].name) == sha3(name)) {
               return patients[p].Creatinite;
            }
        }
    }
    //更新鈉指數
    function updateSodium(string name, uint num) {
        for (uint p = 0; p < patients.length; p++) {
            if (sha3(patients[p].name) == sha3(name)) {
               patients[p].Sodium = num;
            }
        }
    }
    //讀取
    function getSodium(string name) returns(uint Sodium) {
        for (uint p = 0; p < patients.length; p++) {
            if (sha3(patients[p].name) == sha3(name)) {
               return patients[p].Sodium;
            }
        }
    }

    //更新鉀指數
    function updatePotassium(bytes32 name, uint num) {
        for (uint p = 0; p < patients.length; p++) {
            if (sha3(patients[p].name) == sha3(name)) {
               patients[p].Potassium = num;
            }
        }
    }
    //讀取
    function getPotassium(string name) returns(uint Potassium) {
        for (uint p = 0; p < patients.length; p++) {
            if (sha3(patients[p].name) == sha3(name)) {
               return patients[p].Potassium;
            }
        }
    }

    //更新腎功能
    function updateeGFR(string name, uint num) {
        for (uint p = 0; p < patients.length; p++) {
            if (sha3(patients[p].name) == sha3(name)) {
               patients[p].eGFR = num;
            }
        }
    }
    //讀取
    function geteGFR(string name) returns(uint eGFR) {
        for (uint p = 0; p < patients.length; p++) {
            if (sha3(patients[p].name) == sha3(name)) {
               return patients[p].eGFR;
            }
        }
    }
   //更新尿素
    function updateeUrea(string name, uint num) {
        for (uint p = 0; p < patients.length; p++) {
            if (sha3(patients[p].name) == sha3(name)) {
               patients[p].Urea = num;
            }
        }
    }
    //讀取
    function getUrea(string name) returns(uint Urea) {
        for (uint p = 0; p < patients.length; p++) {
            if (sha3(patients[p].name) == sha3(name)) {
               return patients[p].Urea;
            }
        }
    }


    function test() constant returns(uint16 []) {
        return aa;
    }


    //獲得所有病人
    function allPatients(uint16 i) constant returns(string, uint, uint, uint) {
        return (patients[i].name, patients[i].weight ,patients[i].height, patients[i].NHSNum);
    }
}
```



