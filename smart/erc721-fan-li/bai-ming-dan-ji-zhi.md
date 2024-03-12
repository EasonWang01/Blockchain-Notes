---
description: 白名單機制
---

# 白名單機制

目前有三種做法，以下先談到搭配後端與合約的簽名驗證做法

## 1. 後端簽名，合約驗證

1.生成私鑰和對應地址：首先，你需要生成一對以太坊私鑰和公鑰，然後從公鑰導出對應的以太坊地址。這個地址將被用於識別簽名者。重要的是，私鑰必須保密，只能由授權的個人或系統持有。

2.將地址設置為合約中的root：在部署或初始化智能合約時，你需要將這個生成的以太坊地址設置為合約中的root變量。這個地址代表了被授權進行特定操作（如白名單鑄幣）的實體。

3.在後端使用私鑰進行簽名：在後端，當用戶請求鑄造代幣時，你需要使用私鑰對用戶的地址或其他相關數據進行簽名。這個簽名後續會被用戶提交給智能合約。

4.在智能合約中驗證簽名：當智能合約接收到用戶提交的簽名時，它會使用hash.recover(signature)方法從簽名中恢覆出地址，並檢查這個地址是否與存儲在root中的地址相匹配。如果匹配，則驗證成功，表明簽名是由持有對應私鑰的實體生成的，用戶便被授權進行鑄幣操作。

後端

> 生成簽名

```javascript
function backendSign(address, contractName) {
  try {
    const messageHash = web3.utils.soliditySha3(
      { type: "address", value: address }, // 發出交易的人的地址
      { type: "string", value: contractName } // 合約的名稱
    );
    const signature = EthCrypto.sign(
      identity.privateKey, // privateKey
      messageHash // hash of message
    );
    return signature; // 填入 mint 交易的簽名
  } catch (err) {
    return "address not valid"
  }
}
```

合約端

> 呼叫 mint 時傳入後端生成的簽名

<pre class="language-solidity"><code class="lang-solidity">function initialize(address root_, uint256 reserveAmount, uint256 price_, string calldata unrevealedURI) external onlyOwner{
  require(!initialized, "only initialized once");
  initialized = true;
  root = root_;
}

function mint(bytes memory _signature) external nonReentrant payable{
  require(msg.value == price, "Price not correct");
  require(totalSupply() + devReserve &#x3C; collectionSize, "Reach max supply");
  _verifyAndMint(_signature, msg.sender);
}

function _verifyAndMint(bytes memory _signature, address account) internal{
  bytes32 msgHash = keccak256(abi.encodePacked(account, name()));
  require(isValidSignature(msgHash, _signature), "Not authorized to mint");
  ...
}
<strong>
</strong><strong>function isValidSignature(bytes32 hash, bytes memory signature) public view returns (bool isValid) {
</strong>  return hash.recover(signature) == root;
}
</code></pre>
