---
description: 地址格式種類
---

# BTC 地址格式種類

最早的地址格式為：P2PK 但已經沒在用，目前 p2pkh 為主要，佔了 43%

主要有以下幾種

```
P2PKH,
P2WPKH,
P2TR,
P2SH_P2WPKH,
P2WSH,
P2SH
```

常見地址分辨

```javascript
const p2pkh_data = {
  mainnet_address: '1...',
  testnet_address: 'm...' // 'm' or 'n' on testnet or regtest network
};

const p2wpkh_data = {
  mainnet_address: 'bc1q...', // Length of 42 (SegWit variant of P2PKH)
  testnet_address: 'tb1q...'
};

const p2sh_data = {
  mainnet_address: '3...', // Length of 34
  testnet_address: '2...'
};

const p2wsh_data = {
  mainnet_address: 'bc1q...', // Length of 62 (SegWit variant of P2SH)
}

const p2tr_data = {
  mainnet_address: 'bc1p...',
  testnet_address: 'tb1p...'
};
```

#### Convert public key to bitcoin payment object and address

[https://github.com/unisat-wallet/wallet-sdk/blob/master/src/address/index.ts](https://github.com/unisat-wallet/wallet-sdk/blob/master/src/address/index.ts)

<figure><img src="../.gitbook/assets/截圖 2024-06-16 下午4.03.50.png" alt=""><figcaption><p><a href="https://unchained.com/blog/bitcoin-address-types-compared/">https://unchained.com/blog/bitcoin-address-types-compared/</a></p></figcaption></figure>
