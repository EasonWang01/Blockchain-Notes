# Uniswap

{% embed url="https://jeiwan.net/posts/programming-defi-uniswapv2-1/" %}

相關 AMM 原理：

{% embed url="https://uniswapv3book.com/index.html" %}



## Fee

### Swap Fee

Swap 時的 Fee 會直接加入到 liquidity pool。

UniswapV3 後每個 token pair 會有多個 pool （不同 swap fee) ，所以用戶在 add liquidity 時可以選擇 fee %，但在 UI 上 swap 時不能選擇要用哪個 fee 來 Swap，平台會自動幫你選。

[https://docs.uniswap.org/concepts/protocol/fees#pool-fees-tiers](https://docs.uniswap.org/concepts/protocol/fees#pool-fees-tiers)

[https://stackoverflow.com/questions/71990434/what-is-the-difference-between-these-two-pools-in-uniswap](https://stackoverflow.com/questions/71990434/what-is-the-difference-between-these-two-pools-in-uniswap)

## 無償損失

存入 Pool 內的資產，不論原始兩個 token 價格在後續的時間段上漲或是下跌，未來把 Pool 內的資產取出，換回原本的兩種資產時，其取出後的價值一定會少於當初不把兩種資產存入 Pool 的價值。

公式：

> price\_ratio = 1 時，代表沒漲或疊。price\_ratio = 2，意思類似 ETH 從 1000 USDT 變 2000 USDT，如果兩種資產價值都漲一倍（1000, 10000）變 (2000, 20000) 則 price\_ratio 為 4

```
impermanent_loss = 2 * sqrt(price_ratio) / (1 + price_ratio) — 1
```

公式推導：[https://www.youtube.com/watch?v=jIB4ne9jU04](https://www.youtube.com/watch?v=jIB4ne9jU04)

例如下圖：ETH 上漲兩倍後 impermanent\_loss 為 5.7%

<figure><img src="../.gitbook/assets/截圖 2024-01-23 下午3.08.24.png" alt=""><figcaption></figcaption></figure>

{% embed url="https://docs.uniswap.org/contracts/v2/concepts/advanced-topics/understanding-returns" %}

{% embed url="https://chainbulletin.com/impermanent-loss-explained-with-examples-math" %}
