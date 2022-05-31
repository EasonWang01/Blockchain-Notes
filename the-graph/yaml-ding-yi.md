# yaml 定義

subgraph.yaml 上面定義了 subgraph index 最基礎的架構，包含要索引的網路名稱、開始索引的區塊高度、schema 檔案路徑、索引的合約地址、索引的事件名稱與對應的處理邏輯檔案位置等等。

執行 npx graph deploy 後他首先去找到 subgraph.yaml 檔案，才開始執行索引相關邏輯。

一個範例的 subgraph.yaml 應該放置在項目根路徑下，如下範例結構：

```yaml
specVersion: 0.0.4
description: test
repository: https://github.com/graphprotocol/example-subgraph
schema:
  file: ./schema.graphql
dataSources:
  - kind: ethereum/contract
    name: Apple
    network: mainnet
    source:
      address: '0x2E6454222354BB4F5c8a05B3b30A929361cf77eC'
      abi: Apple
      startBlock: 222
    mapping:
      kind: ethereum/events
      apiVersion: 0.0.6
      language: wasm/assemblyscript
      entities:
        - Apple
      abis:
        - name: Apple
          file: ./abis/Apple.json
      eventHandlers:
        - event: NewApple(uint256,address,string,string)
          handler: handleNewApple
        - event: UpdatedApple(uint256,address,string,string)
          handler: handleUpdatedApple
      callHandlers:
        - function: createApple(string,string)
          handler: handleCreateApple
      blockHandlers:
        - handler: handleBlock
        - handler: handleBlockWithCall
          filter:
            kind: call
      file: ./src/mapping.ts
```

我們主要需要修改的欄位有 address, startBlock, entities, abis, eventHandlers, file。

* address: 索引合約地址，可以不填入，例如 ERC721 合約（要索引多個合約從 -kind 整個欄位繼續往下增加即可）
* startBlock: 開始索引的區塊高度
* entities: 的話於 schema 章節會詳細講到
* abis: 索引合約的 abi
* eventHandlers: 使用 event 來索引
* file: 對應的 mapping 邏輯檔案位置

而一般都會使用 eventHandlers 來處理索引，任何有 emit 事件的 solidity function 都可以使用此種方式索引，而 callHandlers 需要節點支持 trace\_route api 才能（例如 parity client)，而 blockHandlers 會對每個新區塊進行索引，所能拿到的資料主要只有 block id 而已。

ERC721 subgraph: [https://github.com/wighawag/eip721-subgraph/blob/master/subgraph.yaml](https://github.com/wighawag/eip721-subgraph/blob/master/subgraph.yaml)

[https://thegraph.com/docs/en/developer/create-subgraph-hosted/#block-handlers](https://thegraph.com/docs/en/developer/create-subgraph-hosted/#block-handlers)
