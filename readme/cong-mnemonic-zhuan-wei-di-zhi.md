# 從 Mnemonic 轉為地址

從註記詞轉為地址可以參考 BIP44:

{% embed url="https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki" %}

{% embed url="https://github.com/satoshilabs/slips/blob/master/slip-0044.md" %}

## 原理

1.Mnemonic 為 12 個英文單字，會先按照 BIP39 轉為 Seed（使用 PBKDF2 function）

轉為 Seed 的過程為 one-way hashing，不可逆，意思為不可從  private key 轉回 Mnemonic。\
[\
Mnemonic Seed - A simple explanation of BIP39.![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAMAAAAoLQ9TAAAAWlBMVEX/////ZgD/hDH/jD//ijz/eyNvb2+Wlpb+/v4tLS3/9u/x8fH/cBAyMjL29vb/mVTp6en/8Ob/r3pUVFQ5OTnf39//uIljPCL/pGj/fyrGxsZZUUuYThxFRUWV5VjAAAAAXklEQVR4AWXIQRLBQBQFwH7AfIEEiOD+17SUKr1s/8RimRUAayAAAZutmGvFzjwQ5joF7MGBYwcEJ32vAAPOC5fiCgShK7cG7gjloYAYnyZefkOJ0how5p0BH5ibGl/zIwKmEKIt4wAAAABJRU5ErkJggg==)Learn Me A Bitcoin_https://learnmeabitcoin.com › technical › mnemonic_](https://learnmeabitcoin.com/technical/mnemonic)

2.之後從 Seed derive 出地址的private key buffer（不同 path 可以產生不同地址）

```
btcRoot.derivePath("m/44'/3'/0'/0/0")
```

[https://learnmeabitcoin.com/technical/derivation-paths](https://learnmeabitcoin.com/technical/derivation-paths)

3.之後在 private key buffer 前方加上在 version byte

4.將結果經過兩次 sha256，取前四個 byte 為 checksum

5.之後將原本的 private key buffer 最後串上第四部的 checksum

6.使用 base58 encode 後即為 WIF 的 private key。

## 範例:

以下產生 Doge chain 的地址

```javascript
const bitcoin = require('bitcoinjs-lib');
const ecc = require('tiny-secp256k1')
const { BIP32Factory } = require('bip32')
const bip39 = require('bip39');
const bip32 = BIP32Factory(ecc)
const { Signer, SignerAsync, ECPairInterface, ECPairFactory, ECPairAPI, TinySecp256k1Interface } = require('ecpair');
const ECPair = ECPairFactory(ecc);
const bs58 = require('bs58')

const btcMnemonic = '...';
const dogeVersionByte = 0x9E;

const btcSeed = bip39.mnemonicToSeedSync(btcMnemonic);
const btcRoot = bip32.fromSeed(btcSeed);
const btcPrivateKey = btcRoot.derivePath("m/44'/3'/0'/0/0").toWIF(); // Replace the path as needed

const btcPrivateKeyBuffer = ECPair.fromWIF(btcPrivateKey).privateKey;
const dogePrivateKeyBuffer = Buffer.concat([Buffer.from([dogeVersionByte]), btcPrivateKeyBuffer]);

const firstSha256 = bitcoin.crypto.sha256(dogePrivateKeyBuffer);
const secondSha256 = bitcoin.crypto.sha256(firstSha256);
const checksum = secondSha256.slice(0, 4);

const dogePrivateKeyWithChecksum = Buffer.concat([dogePrivateKeyBuffer, checksum]);


const dogePrivateKeyWIF = bs58.encode(dogePrivateKeyWithChecksum);

console.log('DOGE Private Key:', dogePrivateKeyWIF);
```

* `m`: This indicates that it's a BIP32 mnemonic code (i.e., a seed phrase).
* `44'`: The first number (44) represents the BIP44 purpose. Purpose 44 is used for cryptocurrencies. The apostrophe ('), which is a hardened derivation, ensures that child keys are derived using hardened keys.
* `3'`: The second number (3) represents the coin type for Dogecoin. Coin type 3 is commonly used for Dogecoin according to the BIP44 specification.
* `0'`: The third number (0) is the account level. This is where you can specify different accounts within your wallet, but in this case, it's set to 0.
* `0`: The next two numbers (0/0) represent the index of the external (receiving) addresses. The first 0 indicates the account's external addresses, and the second 0 indicates the first receiving address.

## privateKeyPrefix

[https://github.com/walletgeneratornet/WalletGenerator.net/blob/36cefb15c625f86c1427e9a17c2bb8d5140918a4/src/janin.currency.js#L196](https://github.com/walletgeneratornet/WalletGenerator.net/blob/36cefb15c625f86c1427e9a17c2bb8d5140918a4/src/janin.currency.js#L196)\
