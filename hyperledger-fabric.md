# #簡介
Hyerledger下有幾個專案
https://www.hyperledger.org/community/projects

但其中只有 Fabric比較成熟


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


#### #使用bluemix(在以下網址申請帳號之後認證信箱然後再到此網址點選建立)
https://console.au-syd.bluemix.net/catalog/services/blockchain/

