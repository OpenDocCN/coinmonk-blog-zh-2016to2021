# 使用 web3.js 在以太坊上签名和进行交易

> 原文：<https://medium.com/coinmonks/signing-and-making-transactions-on-ethereum-using-web3-js-1b5663207d63?source=collection_archive---------0----------------------->

> 我已经和各种区块链平台合作了一段时间了。如果你想联系我，请给我发邮件到 aaroncolaco.com 的亚伦

![](img/4336c968f21a75bf82b62f07d2dba87c.png)

“Palm tree on the hill overlooking ocean bay surrounded by cliffs” by [Chor Hung Tsang](https://unsplash.com/@chorhung?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

最近的一个用例迫使我在以太坊区块链上进行交易，没有运行我自己的对等体，也没有使用任何[钱包](https://coincodecap.com/category/wallets)或扩展，如[薄雾](https://github.com/ethereum/mist)或 [Metamask](https://metamask.io) 。

[](https://github.com/ethereum/mist) [## 以太坊/薄雾

### 薄雾薄雾。浏览和使用以太坊网络上的应用程序。

github.com](https://github.com/ethereum/mist) [](https://metamask.io/) [## 元掩码

### 将以太坊带到你的浏览器中。

metamask.io](https://metamask.io/) 

我将概述我是如何使用一个密钥库文件和 [web3.js](https://github.com/ethereum/web3.js) 来做这件事的。

我联系了 Infura 的同行来测试这一点。这是免费的，如果你不想运行自己的同行，你应该尝试一下。

[](https://infura.io/) [## Infura -可扩展的区块链基础设施

### 对以太坊 API 和 IPFS 网关的安全、可靠和可伸缩的访问。

infura.io](https://infura.io/) 

我假设您已经知道如何创建密钥库文件。如果没有，看一下[文档](https://github.com/ethereum/go-ethereum/wiki/Managing-your-accounts)(使用 geth)。

我用的是 *node v8.9.3* 和*web 3v 1 . 0 . 0-beta 34*

1.  导入 web3(咄！)

```
const Web3 = require('web3');
```

2.连接到 Infura 的同行

(或者您可以连接到您自己的同事)

```
const web3 = new Web3(new Web3.providers.HttpProvider("https://ropsten.infura.io/<TOKEN>"));
```

3.读取密钥库文件的内容

(为了简单起见，我将复制内容)

```
const keystore = "Contents of keystore file";
```

4.解密密钥库文件

```
const decryptedAccount = web3.eth.accounts.decrypt(keystore, 'PASSWORD');
```

5.创建事务对象

```
var rawTransaction = { "from": "Keystore account id", "to": "Account you want to transfer to", "value": web3.utils.toHex(web3.utils.toWei("0.001", "ether")), "gas": 200000, "chainId": 3};
```

6.签署并发送交易

```
decryptedAccount.signTransaction(rawTransaction) .then(signedTx => web3.eth.sendSignedTransaction(signedTx.rawTransaction)) .then(receipt => console.log("Transaction receipt: ", receipt)) .catch(err => console.error(err));
```

您还可以使用解密的密钥库文件中的私钥对事务进行签名:

```
web3.eth.accounts.signTransaction(rawTransaction, decryptedAccount.privateKey) .then(console.log);
```

完整代码:

```
const Web3 = require('web3'); // connect to any peer; using infura hereconst web3 = new Web3(new Web3.providers.HttpProvider("https://ropsten.infura.io/<TOKEN>")); // contents of keystore file, can do a fs readconst keystore = "Contents of keystore file";const decryptedAccount = web3.eth.accounts.decrypt(keystore, 'PASSWORD'); const rawTransaction = { "from": "Keystore account id", "to": "Account you want to transfer to", "value": web3.utils.toHex(web3.utils.toWei("0.001", "ether")), "gas": 2000, "chainId": 3};decryptedAccount.signTransaction(rawTransaction) .then(signedTx => web3.eth.sendSignedTransaction(signedTx.rawTransaction)) .then(receipt => console.log("Transaction receipt: ", receipt)) .catch(err => console.error(err));// Or sign using private key from decrypted keystore file/*web3.eth.accounts.signTransaction(rawTransaction, decryptedAccount.privateKey) .then(console.log);*/
```

这种方法也适用于移动应用程序，而且比你必须运行自己的同行并在上面签署所有交易要好。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   [面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   杠杆代币的终极指南
*   [AscendEx Staking](https://coincodecap.com/ascendex-staking)|[Bot Ocean Review](https://coincodecap.com/bot-ocean-review)|[最佳比特币钱包](https://coincodecap.com/bitcoin-wallets-india)
*   [Bitget 评论](https://coincodecap.com/bitget-review) | [双子星 vs BlockFi](https://coincodecap.com/gemini-vs-blockfi) | [OKEx 期货交易](https://coincodecap.com/okex-futures-trading)
*   [美国最佳加密交易机器人](https://coincodecap.com/crypto-trading-bots-in-the-us) | [经常性回顾](https://coincodecap.com/changelly-review)
*   [在印度利用加密套利赚取被动收入](https://coincodecap.com/crypto-arbitrage-in-india)
*   [霍比审核](https://coincodecap.com/huobi-review) | [OKEx 保证金交易](https://coincodecap.com/okex-margin-trading) | [期货交易](https://coincodecap.com/futures-trading)
*   [麻雀交换评论](https://coincodecap.com/sparrow-exchange-review) | [纳什交换评论](https://coincodecap.com/nash-exchange-review)