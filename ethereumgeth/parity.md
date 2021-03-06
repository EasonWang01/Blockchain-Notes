# Parity

## 簡介:

目標是成為一個輕量快速的ethereum implementation

> Fast, light, robust Ethereum implementation

## 安裝:

先到以下網站，然後往下拉

[https://github.com/paritytech/parity/releases](https://github.com/paritytech/parity/releases)

選擇對應的作業系統版本安裝。

![](../.gitbook/assets/asd3.png)

安裝好後執行程式，會自動啟動本地Server並開啟網頁  
![](../.gitbook/assets/4234.png)

接著輸入錢包的帳號與密碼

![](../.gitbook/assets/9455.png)下一步，記得把恢復錢包的字串記下，I have written down the phrase 之後即可按下一步。

> 以下recovery phrase僅為範例

![](../.gitbook/assets/8877.png)

windows 把以下路徑加入環境變數

```text
C:\Program Files\Ethcore\Parity
```

mac 把以下路徑加入環境變數

```text
export PATH=/Applications/Parity\ Ethereum.app/Contents/MacOS:$PATH
```

## Ubuntu install from command line

```text
wget http://d1h4xl4cr1h0mo.cloudfront.net/v1.6.8/x86_64-unknown-linux-gnu/parity_1.6.8_amd64.deb

sudo apt install gdebi-core

sudo gdebi parity_1.6.8_amd64.deb
```

## 存放資料夾

```text
MAC  $HOME/Library/Application\ Support/io.parity.ethereum/
Windows %UserProfile%\AppData\Roaming\Parity
Linux ~/.local/share/io.parity.ethereum/
```

## 文件

[https://github.com/paritytech/parity/wiki](https://github.com/paritytech/parity/wiki)

