# #簡介
Hyerledger下有幾個專案
https://www.hyperledger.org/community/projects

但其中只有 Fabric比較成熟

# #hyperledger安裝使用Docker

官方文件先前非常不齊全，大多是IBM上較齊全，但最近官方的Docker安裝已經寫好，所以可以參考


http://hyperledger-fabric.readthedocs.io/en/latest/asset_setup.html

以下是前期需求

```
Go - 最新版
Docker - v1.13 or higher
Docker Compose - v1.8 or higher
Node.js & npm - node v6.9.5 and npm v3.10.10
```

之後啟動Docker後新增一個資料夾

```
mkdir -p my_dev_workspace/hackfest
cd my_dev_workspace/hackfest
```

之後下載環境

```
curl -L https://raw.githubusercontent.com/hyperledger/fabric/master/examples/sfhackfest/sfhackfest.tar.gz
```

啟動六個Docker container
(3 peers, a “solo” orderer, cli and CA)

```
docker-compose -f docker-compose-gettingstarted.yml build

docker-compose -f docker-compose-gettingstarted.yml up -d
```


試著輸入以下來查看是否有六個container

```
docker ps
```

然後輸入以下使用cli這個container 並執行bash指令

```
docker exec -it cli bash
```

進入bash後可輸入以下查看已經加入的節點

```
more results.txt
```
或是查看genesis block

```
more myc1.block
```

然後離開


```
exit
```

接著要下載相關 application source code 與 SDK modules


一樣在剛hackfest
的目錄底

```
curl -OOOOOO https://raw.githubusercontent.com/hyperledger/fabric-sdk-node/v1.0-alpha/examples/balance-transfer/{config.json,deploy.js,helper.js,invoke.js,query.js,package.json}
```

然後輸入

```
npm install
```

目前為止我們就具有使用Fabric的基本環境


# #Asset Transfer with SDK

以下使用SDK進行設定 key value pairs of "a","100" & "b","200".


```
GOPATH=$PWD node deploy.js
```

成功後如下圖

![](/assets/ㄉ.png)


然後從A轉帳100給B

```
node invoke.js
```

![](/assets/螢幕快照 2017-03-21 下午3.00.59.png)

然後查看B的餘額

```
node query.js
```


# #使用Cli

我們已經有了一個channel名稱為`myc1`接著我們可以新增一個`myc2`

再次輸入

```
docker exec -it cli bash
```

新增`myc2`channel

```
CORE_PEER_COMMITTER_LEDGER_ORDERER=orderer:7050 peer channel create -c myc2https://yicheng01.github.io/article/test4.html
```
之後會產生`myc2.block` 這個genesis block

接著把三個節點加入channel

```
CORE_PEER_COMMITTER_LEDGER_ORDERER=orderer:7050 CORE_PEER_ADDRESS=peer0:7051 peer channel join -b myc2.block

CORE_PEER_COMMITTER_LEDGER_ORDERER=orderer:7050 CORE_PEER_ADDRESS=peer1:7051 peer channel join -b myc2.block

CORE_PEER_COMMITTER_LEDGER_ORDERER=orderer:7050 CORE_PEER_ADDRESS=peer2:7051 peer channel join -b myc2.block
```

接著以下這個指令，會使用`mycc`這個chaincode 並且 在channel`myc2`的`peer0`結點部署

```
CORE_PEER_ADDRESS=peer0:7051 CORE_PEER_COMMITTER_LEDGER_ORDERER=orderer:7050 peer chaincode deploy -C myc2 -n mycc -p github.com/hyperledger/fabric/examples -c '{"Args":["init","a","100","b","200"]}'
```

之後轉帳
```
CORE_PEER_ADDRESS=peer0:7051 CORE_PEER_COMMITTER_LEDGER_ORDERER=orderer:7050 peer chaincode invoke -C myc2 -n mycc -c '{"function":"invoke","Args":["move","a","b","10"]}'
```


查詢B餘額
```
CORE_PEER_ADDRESS=peer0:7051 CORE_PEER_COMMITTER_LEDGER_ORDERER=orderer:7050 peer chaincode query -C myc2 -n mycc -c '{"function":"invoke","Args":["query","a"]}'
```


-----------

#以下為舊資料


# #安裝環境

官方有提供三種方式
https://www.ibm.com/developerworks/cloud/library/cl-ibm-blockchain-101-quick-start-guide-for-developers-bluemix-trs/index.html#2-3



# #寫chaincode

####1.本地安裝

https://github.com/EasonWang01/learn-chaincode/blob/master/docs/setup.md

#### #golang環境變數配置(gopath,goroot)
http://www.jianshu.com/p/4e699ff478a5


安裝好go後要設置gopath這個是我們工作路徑，之後

```
mkdir -p $GOPATH/src/github.com/hyperledger

cd $GOPATH/src/github.com/hyperledger

git clone -b v0.5-developer-preview https://github.com/hyperledger-archives/fabric.git
```

再來把https://github.com/EasonWang01/learn-chaincode

fork後clone入你的目錄下

```
cd $GOPATH

mkdir -p src/github.com/<YOUR_GITHUB_ID_HERE>/

cd src/github.com/<YOUR_GITHUB_ID_HERE>/

git clone -b v2.0 https://github.com/<YOUR_GITHUB_ID_HERE>/learn-chaincode.git
```

之後可以build

```
cd $GOPATH/src/github.com/<YOUR_GITHUB_ID_HERE>/learn-chaincode/start
go build ./
```


>記得0.5版不會有問題，0.6在build時會出現
```
./chaincode_start.go:34: cannot use new(SimpleChaincode) (type *SimpleChaincode) as type shim.Chaincode in argument to shim.Start:
```






# #與Blockchain互動


https://console.ng.bluemix.net/docs/services/blockchain/etn_sdk.html

https://github.com/IBM-Blockchain/learn-chaincode


####先登入一個使用者

如果使用bluemix可在此查看預設的ID


![](/assets/螢幕快照 2017-02-24 上午9.20.44.png)


如果是local部署可查看
https://github.com/hyperledger/fabric/blob/v0.6/membersrvc/membersrvc.yaml#L199

之後使用postman等工具像節點url發出request

![](/assets/螢幕快照 2017-02-24 上午9.26.09.png)

####部署chaincode

官方目前規定要部署必須將code先放在一個公開的repo上(ex:github)



#### #使用bluemix(在以下網址申請帳號之後認證信箱然後再到此網址點選建立)
https://console.au-syd.bluemix.net/catalog/services/blockchain/

