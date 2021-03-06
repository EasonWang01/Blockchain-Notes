# 門羅幣/Monero \(XMR\)

## 官方挖礦程式

[https://getmonero.org/downloads/](https://getmonero.org/downloads/)

下載後解壓縮並點擊`monero-wallet-gui.exe`

## ![](../.gitbook/assets/sd.png)

## CoinHive教學

一個可以使用瀏覽器挖礦的服務

[https://coinhive.com](https://coinhive.com)

1.先到網站註冊

> 建議用gmail許多郵箱收不到確認信

2.之後可使用

1.[https://coinhive.com/documentation/miner](https://coinhive.com/documentation/miner)

或是

2.[https://github.com/cazala/coin-hive](https://github.com/cazala/coin-hive)

```javascript
const CoinHive = require('coin-hive');

(async () => {

  // Create miner
  const miner = await CoinHive('FacAWlIy3OrBsWVs88bKr2WwxZ8WnBlS'); // CoinHive's Site Key

  // Start miner
  await miner.start();

  // Listen on events
  miner.on('found', () => console.log('Found!'))
  miner.on('accepted', () => console.log('Accepted!'))
  miner.on('update', data => console.log(`
    Hashes per second: ${data.hashesPerSecond}
    Total hashes: ${data.totalHashes}
    Accepted hashes: ${data.acceptedHashes}
  `));

  // Stop miner
  // setTimeout(async () => await miner.stop(), 60000);
})();
```

進行挖礦

3.或是使用

```text
https://authedmine.com/media/miner.html?key={填入你的key}
```

3.之後可到dashboard網頁查看

[https://coinhive.com/dashboard](https://coinhive.com/dashboard)

其他可參考此

[https://lafudoci.gitbooks.io/monero-xmr/content/mining-tool.html](https://lafudoci.gitbooks.io/monero-xmr/content/mining-tool.html)

