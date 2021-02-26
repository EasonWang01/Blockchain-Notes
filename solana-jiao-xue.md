---
description: Solana 是一個類似於 Ethereum 的公鏈，底層是 Rust
---

# Solana 教學

## 安裝

```text
1. sh -c "$(curl -sSfL https://release.solana.com/v1.5.8/install)"

2. 加入環境變數到 ~/.zshrc
vim ~/.zshrc
export PATH="/Users/easonwang/.local/share/solana/install/active_release/bin:$PATH"
```

## 使用

查看目前設置：

```text
solana config get
```

## SPL Token Program

solana 的預設 library 之一，方面我們創建 token 相關的應用。

#### 安裝

```text
cargo install spl-token-cli
```

#### 改變網路環境

```text
solana config set --url https://testnet.solana.com
```

#### 創建新的 account keypair

```text
solana-keygen new
```

之後會產生 /Users/easonwang/.config/solana/id.json 即為你的 private key，可以引入 sollet 網頁錢包。





