# ethereum PoS 節點架設

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

{% embed url="https://docs.prylabs.network/docs/concepts/nodes-networks" %}

## 實際架設範例

以下使用 Prysm

1.需要先安裝 bazel (專案建構工具)

`brew install bazelisk`

2.安裝 Prysm 並編譯可執行節點檔案

```
bazel build //cmd/beacon-chain:beacon-chain --config=release
bazel build //cmd/validator:validator --config=release
```

{% embed url="https://docs.prylabs.network/docs/install/install-with-bazel#install-bazel-using-bazelisk" %}

### 執行節點

1.執行 execution node

```
./geth --mainnet --http --http.api eth,net,engine,admin --authrpc.jwtsecret=<PATH_TO_JWT_FILE> 
```

> \--execution-endpoint 為 Geth 的 RPC endpoint

```
bazel run //cmd/beacon-chain --config=release --execution-endpoint=<YOUR_ETH_EXECUTION_NODE_ENDPOINT> --mainnet
```

完整教學：\
[https://docs.prylabs.network/docs/advanced/proof-of-stake-devnet#manual-setup-built-from-source](https://docs.prylabs.network/docs/advanced/proof-of-stake-devnet#manual-setup-built-from-source)

> 執行 beacon node 後要等個幾分鐘，開始 sync 後才不會有類似如下的 error
>
> ```
> ERROR execution: Beacon node is not respecting the follow distance. EL client is syncing. lastBlockNumber=0
> ```

其他參考：

{% embed url="https://github.com/rzmahmood/ethereum-pos-testnet" %}

## Validator lifecycle

每個 slot 為 12 秒，一個 epoch 為 32 個 slot：6.4 分鐘。

文件描述特定 epoch 後 beacon chain 會執行特定事項 (beacon state transition)。

[https://notes.ethereum.org/7CFxjwMgQSWOHIxLgJP2Bw#A-note-on-Ethereum-20-phase-0-validator-lifecycle](https://notes.ethereum.org/7CFxjwMgQSWOHIxLgJP2Bw#A-note-on-Ethereum-20-phase-0-validator-lifecycle)

## PoS deposit 合約

此合約讓大家可以呼叫 deposit()，支付 32eth 同時，並且放入參數，為成為 PoS validator 必要條件，在此合約成功 deposit 後會 emit 事件，ETH beacon chain 監聽到後會把此申請人加入 queue。

```
bytes calldata pubkey,
bytes calldata withdrawal_credentials,
bytes calldata signature,
bytes32 deposit_data_root
```

之後 deposit function 用 merkle tree 的方式去做 verify。

> deposit\_date\_root 在 cli tool 產生的deposit.json 內，其使用如下參數：validator's public key, withdrawal credentials, the amount of ETH being staked, and the validator's signature.
>
> [https://github.com/ethereum/staking-deposit-cli](https://github.com/ethereum/staking-deposit-cli)

<figure><img src=".gitbook/assets/截圖 2024-02-29 下午2.02.54.png" alt=""><figcaption></figcaption></figure>
