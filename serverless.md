我們會使用到AWS Lambda 與API Gateway

## 前言

兩個的用途分別為，Lambda可以讓我們寫function，API Gateway用來寫path與http method讓別人發出某個對應request時去執行Lambda function

## 實作

1.前往 AWS Lambda

2.選左上的`Create a Lambda function`，之後選擇右上的 Author from scratch

3.填寫function名稱，runtime選擇Node.js

將code部分改為

```
exports.handler = function(event, context) {
  context.succeed("你好!");
};
```

有關handler function的說明  
[http://docs.aws.amazon.com/lambda/latest/dg/nodejs-prog-model-handler.html](http://docs.aws.amazon.com/lambda/latest/dg/nodejs-prog-model-handler.html)

> 如還沒建立IAM須先建立

4.拉到下面Role選單選擇`Create a custom role`然後會跳出一個視窗點選`Allow`，這樣Role即是`lambda_basic_execution`

> 如還沒建立IAM須先建立

5.點選Next並建立，之後可點擊`Test`

6.再來前往AWS 的API gateway服務，點選`get start`，之後選擇`New API`選項，並填寫名稱

7.點選`Actions`的下拉選單，選擇`Create resource`，輸入path與名稱後按確定

8.點選`Actions`的下拉選單，選擇`Create method`，然後下拉選單選擇`POST`，然後勾選`Lambda Function`，之後往下選擇`Lambda Region`，輸入剛才的`Lambda Function name`即可選擇`Save`

9.點選閃電圖案的`Test`然後下拉點選`Test`

## 修改Lambda function

回到aws 的 Lambda 點選剛才創建的function兩下，進入修改code的地方

```
exports.handler = function(event, context) {


var http = require('http');
  http.get({
      host: 'www.chinatimes.com',
      path: '/'
  }, function(response) {
         var body = '';
         response.on('data', function(d) {
          body += d;
    });
        response.on('end', function() {
        context.succeed(body);
    });
  });

};
```

即可使用node.js模組

或是可使用第三方的模組，並且與資料庫溝通

這時先開啟terminal

```
mkdir lambda_test

cd lambdaTest

npm init(記得先安裝好Node.js)

之後新增一個index.js並且存入剛才的資料夾
```

index.js

```
var mongo = require('mongodb');
var Server = mongo.Server;
var Db = mongo.Db;

var server = new Server('ds013898.mlab.com',13898, {auto_reconnect : true});
var db = new Db('forclass', server);

exports.handler = function(event, context) {

db.open(function(err, client) {
    client.authenticate('forclass1', 'test123', function(err, success) {
        if(success){
        console.log("connect success")
        var cursor = db.collection('articles').find();

        cursor.each(function(err, doc) {

         console.log(doc);

         context.succeed(doc);
        db.close();
   });
      }else{
          console.log("client.authenticate error")
      };

    });
});
}
```

然後輸入

```
npm install mongodb
```

> 記得把上面new Server的URL與 改為自己的  
> client.authenticate 改為mLab上的user密碼
>
> 如果還沒申請mLab帳戶可以先去[https://mlab.com/home，然後註冊帳號並且新增使用者帳號以及資料庫，然後點擊進去新增一個collection插入一個document為以下資料](https://mlab.com/home，然後註冊帳號並且新增使用者帳號以及資料庫，然後點擊進去新增一個collection插入一個document為以下資料)

```
{
    "hi": 123,
    "Hello": 456
}
```

可以先本地端測試，把exports.handler與context.succeed\(doc\);  
註解掉即可

之後輸入`open .` 把資料夾的內容index.js和node\_modules壓縮成zip

> 注意:不可直接壓縮資料夾，要進入資料夾後選擇index.js和node\_modules再按壓縮，因為index.js必須在zip檔案的根目錄

回到AWS Lambda上的function，將`Code entry type`旁的選單選擇為`Upload a ZIP file` 然後把剛才的ZIP檔案拉上去

![](/assets/螢幕快照 2017-03-21 上午10.50.01.png)

之後點選TEST即可下拉看結果

> 如果下方出現Timeout 3s 可把程式碼中的console拿掉  
> 或是修改configure的Advance的 setting 中 timeout時間

![](/assets/螢幕快照 2017-03-21 上午10.58.44.png)

再來前往API Gateway

點選藍色的`Create API`

點選`Actions`選單，點選`Create Method`

然後選單選擇`GET`，右方選擇Lambda Function，選擇地區，輸入function名稱，然後點選`save`

![](/assets/螢幕快照 2017-03-21 上午11.04.08.png)

選擇`Deploy API`，選擇new stage然後輸入名稱

過幾秒他會跳出`invoke url`，如下圖

![](/assets/螢幕快照 2017-02-12 下午6.24.39.png)  
點選左側Resource在點選Action即可修改API gateway

記得之後如果修改要重新再`deploy`

# 查看Logs

到AWS 的cloudwatch點選左側的`Logs`即可看到

# 使用serverless的Node.js第三方模組

[https://serverless.com/framework/docs/](https://serverless.com/framework/docs/)

```
npm install serverless -g
```

```
mkdir aws-nodejs

cd aws-nodejs

serverless create -t aws-nodejs
```

之後資料夾內新增了兩個檔案`handler.js`與`serverless.yml`

之後回到aws 然後點選`service`中的`IAM`，左側點選`Users`，之後選`Add user`然後往下拉兩個打勾，之後下一步

選Attach existing policies 然後下方勾選  A[dministratorAcces](https://console.aws.amazon.com/iam/home?region=ap-northeast-1#/policies/arn%3Aaws%3Aiam%3A%3Aaws%3Apolicy%2FAdministratorAccess) 然後點選 `next 之後選Create User`

按下`Create Access Key`按鈕

這邊記得把`Access key ID`跟`Secret access key`記下來

然後到terminal將這兩個加入電腦環境變數

```
export AWS_ACCESS_KEY_ID=填入你的key

export AWS_SECRET_ACCESS_KEY=填入你的access key
```

如果忘記可在建立一個

![](/assets/螢幕快照 2017-03-21 上午11.17.50.png)

然後點選左側選單`Users`點擊剛創建的user，然後選擇`Permissios` Tab 點選`Add permissions` 然後選擇`Attach existing policies directly`

在輸入框輸入`AdministratorAccess`然後把他加入

然後修改`serverless.yml` 把region的`#`拿掉，後面改為

```
region: ap-northeast-1
```

並且把function下面的event註解拿掉

> \(yml等描述檔很注重對齊與空格，如果看到indent相關錯誤可以先去查範例然後來檢查\)

```
    events:
      - http:
          path: users/create
          method: get
```

完整版

```
service: aws-nodejs # NOTE: update this with your service name

# You can pin your service to only deploy with a specific Serverless version
# Check out our docs for more details
# frameworkVersion: "=X.X.X"

provider:
  name: aws
  runtime: nodejs4.3

# you can overwrite defaults here
#  stage: dev
  region: ap-northeast-1

functions:
  hello:
    handler: handler.hello
    events:
      - http:
          path: users/create
          method: get
```

最後

```
serverless deploy
```

成功後會給你一個可以去request的連結  
![](/assets/螢幕快照 2017-02-12 下午6.25.00.png)

回到AWS lambda上看到多出一個function

API gateway也會多出一個

## 測試

於terminal輸入

測試本地

```
serverless invoke local --function hello
```



