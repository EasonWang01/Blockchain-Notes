---
description: 地址格式種類
---

# BTC 地址格式種類

主要有以下幾種

```
P2PKH,
P2WPKH,
P2TR,
P2SH_P2WPKH,
P2WSH,
P2SH
```

地址分辨

```javascript
const p2wpkh_data = {
  mainnet_address: 'bc1q...',
  testnet_address: 'tb1q...'
};

const p2sh_data = {
  mainnet_address: '3...',
  testnet_address: '2...'
};

const p2tr_data = {
  mainnet_address: 'bc1p...',
  testnet_address: 'tb1p...'
};

const p2pkh_data = {
  mainnet_address: '1...',
  testnet_address: 'm...' // 'm' or 'n' on testnet or regtest network
};
```
