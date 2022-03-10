# ERC-721 範例

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
		maxBatchMint, 
		collectionSize) {}
        function testmint(uint amount) external nonReentrant payable{
		require(msg.value == price, "Price is not correct");
		_safeMint(msg.sender, amount);
	}
}
```

## Contract level data

在合約使用public view 設置 opensea 上 collection 的基本資訊。

> 如果沒設置之後 mint 後再到 opensea 設置也可以

[https://docs.opensea.io/docs/contract-level-metadata](https://docs.opensea.io/docs/contract-level-metadata)
