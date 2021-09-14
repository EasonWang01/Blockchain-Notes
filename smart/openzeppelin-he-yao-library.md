---
description: 引用其函式庫後快速產生 ERC-20 或 ERC-721、ERC-777、ERC-1155 等合約
---

# OpenZeppelin 合約 library

{% embed url="https://github.com/OpenZeppelin/openzeppelin-contracts" %}

## 繼承方法

使用 is 繼承，之後可以使用其內的方法，直接呼叫。

[https://docs.openzeppelin.com/contracts/2.x/api/token/erc721](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721)

```javascript
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/utils/Strings.sol";

contract Test is ERC721Enumerable, ReentrancyGuard, Ownable {
 ....
}

```

## 使用 Remix

![](../.gitbook/assets/jie-tu-20210505-shang-wu-9.53.16.png)

或是

![](../.gitbook/assets/jie-tu-20210505-shang-wu-9.53.38.png)

