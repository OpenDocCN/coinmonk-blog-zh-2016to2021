# 如何创建自己的加密货币！！！

> 原文：<https://medium.com/coinmonks/part-3-how-to-create-your-own-cryptocurrency-bb6c75353218?source=collection_archive---------1----------------------->

第 3 部分:区块链如何识别黑客何时更改了数据块中的数据？

![](img/5ab180458640f476554419969d926c32.png)

pic source [here](https://previews.123rf.com/images/peshkov/peshkov1903/peshkov190300086/118031824-hacker-using-abstract-bitcoin-interface-hacking-and-cryptocurrency-concept.jpg)

欢迎回来伙计们🤗🤗..希望每个人都做得很好，看看我以前的博客，如果有人错过了，请在这里阅读它，这样你就可以享受阅读这篇博客的乐趣。

现在，这个博客有什么特别的🤔🤔？我们将见证当黑客试图改变一个块中的数据时，区块链有多安全😯！！除此之外，我们还将简化之前的代码...让我们先简化我们之前的代码，这意味着我们要做一些改变😨其中功能保持不变。相信我，它们真的是很小的变化😜

# 向区块链添加块时用块替换字典:

自从链接以来，我改变了区块链类中的 add()方法如下

add() method in BlockChain class

up of blocks (class)是一种好的编码方法，而不是通过字典。由于这种变化，我们必须改变 mine()方法，如下所示

mine() method in blockchain

在这里我改变了 self.chain[-1]。hash()，因为 self.chain[-1]本身就是一个块(因为我们正在添加一个块),所以我调用了类 block 的 hash()来获得该块的散列

我已经改变了下面的主要方法，以获得更好的输出视觉效果

main() method

现在，您可以通过在命令提示符下导航到该目录并输入 python **<文件名>来查看输出。py** 如下图

output

好了…这就是我们所有的代码变更😊…因此，在看到输出时，我们可以看到功能保持不变。

# 区块链如何识别何时有人更改块中的数据🤔

这个问题很酷，因为每个人都知道区块链是安全的🔒数据是无法改变的，但很多人不知道区块链是如何做到的。让我们讨论并验证这一点😊

我将在区块链类中引入' *isValid()* '方法，如下所示

isValid() method in blockchain

该方法具有这样的逻辑，即块 N 的先前散列应该等于块 N-1 的散列，该散列是在检查时新计算的，并且该散列应该与难度相匹配。这就是区块链如何在更简单的级别上变得安全。

让我们通过调用 is valid()函数来检查我们的区块链是否有效。现在主函数看起来像这样

main method

如果我们的区块链是安全的，那么'*区块链是有效的'*将被打印出来，否则它将是'*是有效的:假'。*

output showing blockchain is valid

让我们尝试改变第三块中的数据，并尝试验证区块链是否表明它是无效的

The main method to change data in the third block

output showing blockchain is not valid with block number

您可以尝试更改任何其他块数据。想要源代码的可以在这里访问更新后的代码[。](https://github.com/AnanthaPerumal/BlockChain)

非常感谢您在我的博客中花费宝贵的时间。请以你认为值得的方式，以 1-50 的比率鼓掌来支持我。如果你对这个博客有任何疑问或改进的建议，请在下面评论并分享给那些想了解区块链的人。

请继续关注下一部分。您可以在此找到下一个零件链接[。我会给你一些你能理解的简单的东西😉😉。](https://anantha-perumal.medium.com/part-4-how-to-create-your-own-cryptocurrency-654b20ffc3a6)

祝你愉快😊😊

## 另外，阅读

*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   [顶级 DeFi 项目](/coinmonks/defi-future-10-promising-projects-in-the-defi-world-ff2b697ab006)
*   开发人员的最佳加密 API

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://coincodecap.com?utm_source=coinmonks)