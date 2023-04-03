# 解开以太坊交易的秘密

> 原文：<https://medium.com/coinmonks/discovering-the-secrets-of-an-ethereum-transaction-64febb00935c?source=collection_archive---------1----------------------->

## 用 Python 解码输入数据

![](img/b63e736c5afac46f3f7fd45bcd1322d1.png)

如果你是作为区块链工程师迈出的第一步，一个很好的起点是构建一个解码事务的应用程序，特别是输入数据。解码输入数据是理解交易中实际发生了什么的关键。比如看一下[下面这个交易](https://etherscan.io/tx/0xac80bab0940f061e184b0dda380d994e6fc14ab5d0c6f689035631c81bfe220b)。

您可以看到它包含有关交易的高级细节，包括往来地址、使用的气体、交易价值、签名组件等。但是在这个交易中到底发生了什么呢？换了哪些代币？使用了谁的智能合同？为了真正理解细节，我们需要解码*输入数据*。

## 输入数据…那是什么？

在开始编写代码之前，让我们花点时间了解一下输入数据以及它在区块链上的使用方式。我们将使用上面显示的事务作为我们的例子。

当我们在 [Etherscan](https://etherscan.io/tx/0xac80bab0940f061e184b0dda380d994e6fc14ab5d0c6f689035631c81bfe220b) 上搜索交易时，我们看到有人将`4.195472736637743549 ETH`发送到一个 SushiSwap 智能合约，以换取`20 BOR`令牌。用户可能在 SushiSwap 用户界面中启动了事务，这导致事务由智能合约执行。但是智能合同是如何知道该做什么的呢？你猜对了——输入数据。

输入数据由[ethereum.org](https://ethereum.org/en/developers/docs/data-and-analytics/block-explorers/#transactions)描述为*交易*所需的额外信息。这种信息的结构因交易类型的不同而不同。如果交易是一个`message call`(由一个外部拥有的账户(EOA)签署，或者从一个智能合同到一个 EOA)，输入数据包含正确执行智能合同功能所需的信息。如果是一个`contract creation`事务，输入数据将保存合同字节码和构造函数所需的任何编码参数。如果交易是两个 EOA 之间的`funds transfer`，输入数据将为空，因为不需要额外的信息。

在我们的示例中，事务是一个`message call`,因为我们在一个已部署的智能契约上调用一个函数。输入数据如下所示:

```
0x8803dbee000000000000000000000000000000000000000000000001158e460913d000000000000000000000000000000000000000000000000000003a4837fc5242c4ec00000000000000000000000000000000000000000000000000000000000000a00000000000000000000000009a7ed54b8c2c5816c1800476f5111a1e886575020000000000000000000000000000000000000000000000000000000060cfee3f0000000000000000000000000000000000000000000000000000000000000002000000000000000000000000c02aaa39b223fe8d0a0e5c4f27ead9083c756cc20000000000000000000000003c9d6c1c73b31c837832c72e04d3152f051fc1a9
```

智能协定使用这个十六进制值来确定需要执行哪个函数以及需要哪些参数来确保函数按照 SushiSwap 用户的意图执行。解码后，您会得到函数定义:

```
swapTokensForExactTokens(uint256 amountOut, uint256 amountInMax, address[] path, address to, uint256 deadline)
```

它的论点是:

```
**amountOut**: 20000000000000000000
**amountInMax**: 4199668209374381292
**path**: [0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2](https://etherscan.io/address/0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2), [0x3c9d6c1C73b31c837832c72E04D3152f051fc1A9](https://etherscan.io/address/0x3c9d6c1C73b31c837832c72E04D3152f051fc1A9)
**to**: [0x9A7eD54b8c2c5816C1800476F5111A1e88657502](https://etherscan.io/address/0x9A7eD54b8c2c5816C1800476F5111A1e88657502)
**deadline**: 1624239679
```

但是我们如何使用 Python 将这种十六进制输入转换成纯文本呢？让我们分解代码。

## 解码输入数据

我们将使用 Web3 库与以太坊节点进行交互。有关安装和入门要求，请参见 [Web3 文档](https://web3py.readthedocs.io/en/stable/quickstart.html)。

我们的第一步是通过将事务散列传递给 Web3 的`get_transaction()`方法来获取事务摘要。这将返回一个字典，其中包含与我们的事务相关的信息，如本文开头所示。

接下来我们需要得到智能合约的[应用二进制接口(ABI)](https://en.wikipedia.org/wiki/Application_binary_interface) 。这允许我们创建一个*契约对象*，用于与智能契约进行交互。我们可以通过调用 Etherscan API 来实现这一点。你需要一个 Etherscan API 密钥，可以通过在 [Etherscan 的开发者页面](https://etherscan.io/apis)上设置和注册来免费获得一个。

注意，我们使用事务中的`to`值作为智能合约地址。对于调用智能合约功能的交易，智能合约地址将始终是交易的`to`地址。

接下来，我们将通过将智能契约的地址和 ABI 作为参数传递给`contract()`方法*来创建我们的契约对象。*返回的对象包含许多有用的方法和属性，我们可以用它们来与我们的智能合约进行交互。

一旦我们有了契约对象，我们就使用`decode_function_input()`方法来解码我们的输入。

然后砰！我们做到了。该方法返回一个元组，我们可以将它解包成两个对象。`func_obj` 是我们交易使用的智能合约函数的一个实例。

```
<class 'web3._utils.datatypes.swapTokensForExactTokens'>swapTokensForExactTokens(unit256,united256,address[],unit256)
```

该对象包括智能合约的 ABI、函数名、气体评估工具以及其他我们可以用来构建和调用新交易的方法和属性。

`func_params` 是一个字典，将事务的参数作为键，将它的参数作为值。

解码输入数据后，我们现在可以看到 SushiSwap 用户支付的 ETH 金额、他们收到的 BOR 代币金额、所涉及代币的智能合约地址以及交易的截止日期。这让我们真正了解了在这个交易中发生了什么。

## 接下来…

这项基本技能可应用于许多应用，包括区块链智能、区块链数据分析等。我的下一篇文章将是关于解码事务日志。点击跟随按钮，这样你就不会错过它。

如果你觉得这篇文章有帮助，如果你能留下评论并鼓掌，我们将不胜感激。谢谢大家！

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](/coinmonks/top-3-telegram-channels-for-crypto-traders-in-2021-8385f4411ff4) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   开发人员的最佳加密 API
*   [NFT 十大市场造币集锦](https://coincodecap.com/nft-marketplaces)
*   [AscendEx Staking](https://coincodecap.com/ascendex-staking)|[Bot Ocean Review](https://coincodecap.com/bot-ocean-review)|[最佳比特币钱包](https://coincodecap.com/bitcoin-wallets-india)
*   [Bitget 回顾](https://coincodecap.com/bitget-review) | [双子 vs 区块链](https://coincodecap.com/gemini-vs-blockfi) | [OKEx 期货交易](https://coincodecap.com/okex-futures-trading)
*   [美国最佳加密交易机器人](https://coincodecap.com/crypto-trading-bots-in-the-us) | [经常性回顾](https://coincodecap.com/changelly-review)
*   [在印度利用加密套利赚取被动收入](https://coincodecap.com/crypto-arbitrage-in-india)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [免费加密信号](/coinmonks/free-crypto-signals-48b25e61a8da) | [加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   杠杆代币的终极指南