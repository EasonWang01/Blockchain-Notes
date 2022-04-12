# AAVE、Compound

此兩者為 lending protocol，主要提供 lending 和 borrow 並提供利息。

AAVE 存款後會給你 aToken， Compound 存款後會給你 cToken。

> 存入不同代幣會給你對應不同的 cToken，如下圖

![](<../../.gitbook/assets/截圖 2022-02-21 下午12.09.14.png>)

{% embed url="https://etherscan.io/tokens/label/compound" %}

### aToken 與 cToken 區別

如同上面所述，除了平台的區別外，aToken 的利息是直接給你更多的 aToken，但 cToken 給你的利息不會直接給你 cToken，而是把 cToken 對換回原本的 Token 的兌換率增加。

{% embed url="https://docs.aave.com/developers/v/1.0/developing-on-aave/the-protocol/atokens" %}

{% embed url="https://compound.finance/docs/ctokens" %}

