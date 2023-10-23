# ERC-721 範例

## ERC-721 常用於 NFT 相關合約

其擁有以下標準：

1. **擁有權**
   * `ownerOf(uint256 tokenId) public view returns (address)`: 返回某個指定 `tokenId` 的擁有者。
2. **代幣轉移**
   * `transferFrom(address from, address to, uint256 tokenId) public`: 從一個地址轉移到另一個地址。
   * `safeTransferFrom(address from, address to, uint256 tokenId) public`: 安全地從一個地址轉移到另一個地址，檢查接受方是否可以接收代幣。
   * `safeTransferFrom(address from, address to, uint256 tokenId, bytes data) public`: 帶有額外數據的安全轉移。
3. **授權操作**
   * `approve(address to, uint256 tokenId) public`: 授予某個地址權限移動特定的代幣。
   * `getApproved(uint256 tokenId) public view returns (address)`: 返回某個特定代幣的已批准地址。
   * `setApprovalForAll(address operator, bool approved) public`: 授權或撤銷某個地址轉移某個擁有者的所有代幣的權限。
   * `isApprovedForAll(address owner, address operator) public view returns (bool)`: 查看某個地址是否被授予了轉移另一個地址的所有代幣的權限。
4. **供應和創建**
   * `totalSupply() public view returns (uint256)`: 返回已發行的代幣總數。
   * `mint(address to, uint256 tokenId) public`: 創建並發行一個新的代幣。
5. **事件**
   * `Transfer(address from, address to, uint256 tokenId)`: 當代幣被轉移時觸發。
   * `Approval(address owner, address approved, uint256 tokenId)`: 當某個代幣的授權被更改時觸發。
   * `ApprovalForAll(address owner, address operator, bool approved)`: 當某個地址被授予或撤銷轉移所有代幣的權限時觸發。

## &#x20;自己部署一個 ERC-721 NFT 合約

可以直接用 Remix 部署

```javascript
// SPDX-License-Identifier: MIT
pragma solidity ^0.7.0;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v3.4/contracts/token/ERC721/ERC721.sol";
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v3.4/contracts/utils/Counters.sol";
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v3.4/contracts/access/Ownable.sol";

contract NftExample is ERC721, Ownable {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIds;

    constructor() ERC721("3D-Marketplace", "3D-NFT") {}

    function mintNft(address receiver, string memory tokenURI) external onlyOwner returns (uint256) {
        _tokenIds.increment();

        uint256 newNftTokenId = _tokenIds.current();
        _mint(receiver, newNftTokenId);
        _setTokenURI(newNftTokenId, tokenURI);

        return newNftTokenId;
    }
}
```

{% embed url="https://soliditydeveloper.com/erc-721" %}

## ERC-20 與 ERC-721 Total Supply 意思差別

1. **ERC20**：
   * **定義**：代表**可替代**代幣的標準，意味著每個代幣都是相同的，並且可以互換。例如，所有的USDT或ETH都是一樣的，並且具有相同的價值。
   * **totalSupply**：在ERC20標準中，`totalSupply` 函數返回**該代幣的總供應量。這是一個固定的數字**，表示該代幣的總數量。
2. **ERC721**：
   * **定義**：代表**不可替代**代幣（NFT，Non-Fungible Token）的標準，意味著每個代幣都是獨特的，並且不能互換。例如，數字藝術品、收藏品或其他獨特的項目。
   * **totalSupply**：在ERC721標準中，`totalSupply` 函數返回**當前已經發行的代幣總數**。不同於ERC20，每個ERC721代幣都是獨特的，所以`totalSupply` 更多地表示已發行的獨特項目的數量。

## ERC721A 教學

為 ERC721 的改良版 (多了個 A) 主要用意是讓玩家如果一次 mint 多個 NFT 時可以減少 gas fee， 發明者為 azuki 專案。

可以看到如下表格，當一次 mint 五個的時候，在以前 ERC721 標準下要執行五次 mint function，但 ERC721A 可以在 mint function 內加上第二個參數 (mint 的數量)，讓你一次 mint 多個，減少了不少 gas 花費。

![](<../.gitbook/assets/截圖 2022-03-10 上午10.13.02.png>)

{% embed url="https://www.azuki.com/erc721a" %}

#### 使用方式

```javascript
contract test is Ownable, ERC721A {
	constructor(
	  ...
	) ERC721A(
		name, 
		symbol, 
		collectionSize) {}
        function testmint(uint amount) external nonReentrant payable{
		require(msg.value == price * amount, "Price is not correct");
		_safeMint(msg.sender, amount);
	}
}
```

## Contract level data

在合約使用public view 設置 opensea 上 collection 的基本資訊。

> 如果沒設置之後 mint 後再到 opensea 設置也可以

[https://docs.opensea.io/docs/contract-level-metadata](https://docs.opensea.io/docs/contract-level-metadata)
