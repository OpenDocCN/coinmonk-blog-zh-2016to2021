# 用 JavaScript 理解和构建你自己的小区块链

> 原文：<https://medium.com/coinmonks/understanding-and-building-your-own-tiny-blockchain-in-javascript-a16f2137cfec?source=collection_archive---------0----------------------->

![](img/97f213b357f7ff68361353c5e6142520.png)

区块链背后的理念和技术一直令我着迷。除了区块链技术有能力制造的社会、金融和技术破坏，一段代码可能是这一切背后的简单想法一直让我感到惊讶。

尽管区块链理工大学通常有免费提供的白皮书和更多的论坛、discord 和 IRC 频道，以及我们不知道该怎么做的开发团队，区块链的基本结构可能相当不透明。让我们看看如何构建一个非常简单的块区块链来帮助可视化事务是如何添加的。

**需求:**我在 [Node.js](https://nodejs.org/en/) 中构建了这个，但是如果你愿意，你也可以很容易地在 [Repl.it](https://repl.it/) 上构建它。我们还将利用 sha256 npm 包对数据块进行哈希处理。

如果您是 Node.js 的新手，首先您需要做的是创建一个新目录，创建一个`index.js`文件，然后从您的终端，在您的新目录中运行`npm init -y`。这将创建一个 package.json 文件，它将允许我们安装 sha256 包。运行`npm i sha256`安装 sha256 库。如果您在 Repl.it 上，创建一个新的 Nodejs repl，安装`js-sha256`包，并需要它。

现在我们已经设置好了，是时候创建一个新的类了，这样我们就有了一个模板，我们将使用它来构建每个块。我们简单的区块链上的每个块将包含 5 条数据:块的索引、它创建的精确时间、一些任意数据(对于比特币，这些数据将是比特币的发送者和接收者的地址以及交易的金额)、它前面的块的哈希值以及它本身的哈希值。哈希是一段数据的数字指纹。我们将在后面看到，一旦区块被确认，每个区块的这一部分是如何使任何人都很难改变区块链的。

为了创建我们的块，我使用了一个 JavaScript 类，但是构造函数也能很好地工作，因为这实际上是一个类的全部内容。我设置了构造函数，将除散列之外的所有数据作为参数。然后，该块将查看索引、时间戳、数据和以前的哈希，并生成自己的哈希或指纹。

您可能已经注意到，因为每个块都需要前一个块的散列，所以我们有一个如何创建第一个块的问题。第一个块，称为 Genesis 块，将作为前一个散列得到 0，所以我们必须编写一个函数来创建这个块。

在第 15 行，我使用一个箭头函数基于我们之前写的类创建了一个新的块。我传递给它一个用于索引的`0`，当前时间，一个声明它是 Genesis 块的字符串，以及一个之前的哈希`0`。我们现在有了第一个区块，我们几乎完成了！创世纪区块是特殊的，但其他区块将使用相同的公式前进。

在这里，我编写了一个`nextBlock`函数，它只接受最后一个块和新块的数据作为参数。然后，该函数创建一个新块，并将该新块的索引设置为前一个块的索引并加 1，获取当前时间，然后获取前一个块的散列并将其设置为新块上的`prevHash`。这最后一步虽然只有很少的代码，但却非常重要。这通过散列将每个块链接到它的前一个块。比方说，有人想改变比特币区块链，让他们看起来好像收到了比实际更多的比特币，或者让他们看起来好像花了更少的比特币。如果他们改变了一个数据块，作为数据块创建时包含的数据快照的散列将不再匹配。为了补救这一点，他们不得不重新洗牌。因为所有的块都链接在一起，并指向前一个块的散列，所以这个人还必须改变目标块后面的所有块，以使其看起来合法。

虽然现代计算机可以改变一个块，生成新的哈希，然后为所有后续块生成新的哈希，以便被改变的块被视为合法，但比特币特别实施了一种称为“工作证明”的方法，以减缓新块的确认，这几乎是不可能的。此外，由于区块链所依赖的分布式分类帐网络，一个邪恶的行动者将不得不控制超过 50%的网络，以便他们的伪造区块链被接受。我们的区块链不会被分发或利用工作证明，但它是一个非常聪明的解决问题的方法，这些主题值得自己的解释。

所以现在我们有了生成我们小小的、简单的区块链所需的一切！让我们写一个函数，它可以运行任意多次，添加新的块，然后我们看看它创建了什么。

在第 20 行，我创建了一个新函数，它将创建一个任意长度的新区块链。我创建了一个新的区块链，我们将它存储在一个数组中，我将 genesis 块作为数组中的第一个元素。接下来，我一次创建一个块，将它需要的来自前一个块的数据传递给它，然后将新块放入我们的数组中。完成后，我会记录结果。让我们看一下最初的几个块，看看我们已经创建了什么。

![](img/f34a6c724201f7fd9bc5408e2e67653e.png)

这是我们链中的前 4 个块。注意每个块的`prevHash`值如何与它前面的块的`thisHash`值相匹配。这以类似于链表的方式将所有块链接在一起，并确保篡改区块链极其困难。

在这个例子中，我省略了很多信息，但主要思想是存在的。在我能够想象这些短暂的街区和我久闻其名的区块链之前，这一切对我来说都只是魔法。现在，使用几十行代码，我们已经创建了自己的区块链！

[这里有一份简单区块链的所有代码的副本，如果你想玩的话。](https://repl.it/@riccjohn/Tiny-Blockchain-node)

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   开发人员的最佳加密 API
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [杠杆代币](/coinmonks/leveraged-token-3f5257808b22)终极指南
*   [7 个最佳零费用加密交换平台](https://coincodecap.com/zero-fee-crypto-exchanges)
*   [最佳网上赌场](https://coincodecap.com/best-online-casinos) | [期货交易机器人](/coinmonks/futures-trading-bots-5a282ccee3f5)
*   [分散交易所](https://coincodecap.com/what-are-decentralized-exchanges) | [比特 FIP](https://coincodecap.com/bitbns-fip)
*   [用信用卡购买密码的 10 个最佳地点](https://coincodecap.com/buy-crypto-with-credit-card)
*   [加拿大最佳加密交易机器人](https://coincodecap.com/5-best-crypto-trading-bots-in-canada) | [Bybit vs 币安](https://coincodecap.com/bybit-binance-moonxbt)