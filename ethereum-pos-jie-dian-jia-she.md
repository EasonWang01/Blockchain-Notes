# ethereum 節點架設

\[2024 更新]

The merge (POS) 之後必須要架設兩個 client 才能正常執行節點。

分別為 execution layer（例如 Geth ) 以及 consensus layer (例如 Prysm, Loadstar)

> consensus client (layer) 也稱為 Beacon Node、CL Client

<figure><img src=".gitbook/assets/截圖 2024-02-28 上午11.36.20.png" alt=""><figcaption></figcaption></figure>

{% embed url="https://ethereum.org/developers/docs/nodes-and-clients#consensus-clients" %}

從以下網站可以查看目前哪些節點較多人用：\
[https://clientdiversity.org/#distribution](https://clientdiversity.org/#distribution)

## 實際上有第三種節點

叫做 validator node，可使用  consensus layer 的 client 架設，validator node 用來 stake 32 ETH, 並且存放錢包私鑰、產生區塊等。

> validator node 只與 beacon node 連接

[https://docs.prylabs.network/docs/concepts/nodes-networks](https://docs.prylabs.network/docs/concepts/nodes-networks)
