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

![](<../.gitbook/assets/截圖 2021-05-05 上午9.53.16.png>)

或是

![](<../.gitbook/assets/截圖 2021-05-05 上午9.53.38.png>)

## Access control

可用來賦予不同的地址執行函數的權限。

{% embed url="https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/AccessControl.sol" %}

```javascript
contract token is ERC20, AccessControl {


    grantRole(DEFAULT_ADMIN_ROLE, msg.sender); // 讓 msg.sender 為 DEFAULT_ADMIN_ROLE 
    // _grantRole 的角色為 32byte sha3 算出來的，使用字串來產生 sha3 
    
    hasRole(bytes32 role, address account) // 確認地址擁有權限
  
    bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
    // 使用 onlyRole 來限制某權限角色才可執行
f   function mint(address to, uint256 amount) public onlyRole(MINTER_ROLE) { 

    }
  }
}
```
