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

## Merkle Tree 方式

產生 root

```javascript
const { MerkleTree } = require('merkletreejs');
const keccak256 = require('keccak256');

// List of whitelisted addresses
const whitelistAddresses = [
  '0xAddress1...',
  '0xAddress2...',
  '0xAddress3...',
  // ... more addresses
];

// Generate leaf nodes
const leaves = whitelistAddresses.map(addr => keccak256(addr));
const tree = new MerkleTree(leaves, keccak256, { sortPairs: true });

// Get the root hash of the Merkle tree (to be used in the smart contract)
const rootHash = tree.getRoot().toString('hex');
```

產生 proof

> 用戶發送 mint 前，瀏覽器用個別地址跟 API Server 獲取 proof

```javascript
const address = '0xAddress1...'; // Address to generate proof for
const leaf = keccak256(address);
const proof = tree.getHexProof(leaf);

console.log(proof); // This will be used in the smart contract for verification
```

contract&#x20;

```javascript
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/utils/cryptography/MerkleProof.sol";

contract WhitelistedNFT {
  bytes32 public merkleRoot;

  constructor(bytes32 _merkleRoot) {
    merkleRoot = _merkleRoot;
  }

  function mint(bytes32[] calldata _merkleProof) public {
    // Verify the provided address against the Merkle root
    bytes32 leaf = keccak256(abi.encodePacked(msg.sender));
    require(MerkleProof.verify(_merkleProof, merkleRoot, leaf), "Not in whitelist");

    // Mint NFT logic here...
  }
}
```

可用以下腳本測試

> 記得 leaves 至少要一個以上，不然 getProof 會回傳空 array

```javascript
const { MerkleTree } = require("merkletreejs");
const keccak256 = require("keccak256");
const leaves = ['0xaa2eAbb245944168705e3Ad21C9D266131E296E7', '0xaaA0ea4E952C2a9bB6FDaDf7cBa1a08eb20EE157'].map(x => keccak256(x))
const tree = new MerkleTree(leaves, keccak256)
const root = tree.getRoot().toString('hex')
const leaf = keccak256('0xaa2eAbb245944168705e3Ad21C9D266131E296E7')
const proof = tree.getProof(leaf)
const rootHash = tree.getRoot().toString("hex");
console.log(tree.verify(proof, leaf, rootHash)) // true
```
