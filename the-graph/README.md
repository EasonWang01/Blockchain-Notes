---
description: 一個用來查詢區塊鏈數據的工具
---

# The Graph

使用 graphQL 數據結構，讓你可以監聽區塊鏈節點的事件或數據，並且發佈到 graph node 上面。之後可以讓大家 Query 使用。需要自己寫一個 subgraph 的邏輯，然後發布到 graph node上。官方有提供 名為[ studio node](https://thegraph.com/studio/) 的 graph node。也可以在自己本地架設。

### 官方文件

[https://thegraph.com/docs/en/](https://thegraph.com/docs/en/)

### 本地架設 Graph node 教學

[https://thegraph.academy/developers/local-development/](https://thegraph.academy/developers/local-development/)

### 探索已發佈的 Subgraph

{% embed url="https://thegraph.com/explorer" %}

## 使用 OpenZeppelin 範例

[https://github.com/OpenZeppelin/openzeppelin-subgraphs](https://github.com/OpenZeppelin/openzeppelin-subgraphs)

[https://docs.openzeppelin.com/subgraphs/0.1.x/](https://docs.openzeppelin.com/subgraphs/0.1.x/)

將以上 clone 到你的專案或是 `npm install @openzeppelin/subgraphs`，之後 copy node\_module 裡面 config file，然後放到你的專案內

之後輸入以下（要把 --config 後的路徑改成剛的 config file）

```
npx graph-compiler \
  --config configs/sample.json \
  --include node_modules/@openzeppelin/subgraphs/src/datasources \
  --export-schema \
  --export-subgraph
```

![](<../.gitbook/assets/截圖 2022-02-07 下午3.09.07.png>)

之後要去把 編譯出來的 yaml 改名為 subgraph.yaml，然後增加 network 與 contract address 等 config field

ERC721 的 subgraph.yaml 範例：

```
specVersion: 0.0.2
schema:
  file: erc721.schema.graphql
dataSources:
  - kind: ethereum/contract
    name: erc721
    network: mainnet
    source:
      abi: IERC721
    mapping:
      kind: ethereum/events
      apiVersion: 0.0.5
      language: wasm/assemblyscript
      entities:
        - ERC721Contract
      abis:
        - name: IERC721
          file: ../node_modules/@openzeppelin/contracts/build/contracts/IERC721Metadata.json
      eventHandlers:
        - event: Approval(indexed address,indexed address,indexed uint256)
          handler: handleApproval
        - event: ApprovalForAll(indexed address,indexed address,bool)
          handler: handleApprovalForAll
        - event: Transfer(indexed address,indexed address,indexed uint256)
          handler: handleTransfer
      file: ../node_modules/@openzeppelin/subgraphs/src/datasources/erc721.ts

```

之後看你要部署到 studio 還是自己 host 的節點，如果要部署到 studio 就先去 [https://thegraph.com/studio/](https://thegraph.com/studio/) create subgraph 然後從 cli auth 後 deploy

```
graph deploy --studio <yaml 內 dataSources 的 name>
```

部署到 local 節點就參考：https://thegraph.academy/developers/local-development/