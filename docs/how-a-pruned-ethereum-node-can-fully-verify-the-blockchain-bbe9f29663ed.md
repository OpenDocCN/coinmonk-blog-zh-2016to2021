# 消解神话:一个修剪过的以太坊节点如何充分验证区块链

> 原文：<https://medium.com/coinmonks/how-a-pruned-ethereum-node-can-fully-verify-the-blockchain-bbe9f29663ed?source=collection_archive---------3----------------------->

![](img/f3630b9020ed6a1a12ee3e433521d7ca.png)

source: [shutterstock.com](https://www.shutterstock.com/image-photo/gardener-pruning-trees-shears-on-nature-761866162?irgwc=1&utm_medium=Affiliate&utm_campaign=Hans%20Braxmeier%20und%20Simon%20Steinberger%20GbR&utm_source=44814&utm_term=)

以太坊的批评者担心以太坊区块链太大了。他们担心它会占用太多的存储空间。

存储空间是一个问题，因为如果日常用户无法在笔记本电脑上安装区块链，他们将无法在本地验证区块链。

如果普通用户无法运行**完整节点**，他们将失去体验 crypto 全部潜力的能力。这个**可能**也会让能够运行完整节点的第三方对以太坊区块链未来的发展有更大的影响力。

在这篇文章中，我将消除日常用户因为存储需求而无法运行完全验证的以太坊节点的神话。我将描述一个修剪过的以太坊节点是如何被完全验证的。

## **完整节点是什么意思？**

如果您运行完整节点，这意味着您的以太坊客户端**构建并验证了**自 genesis 块以来您的本地区块链(即您笔记本电脑上的区块链)的当前状态，并且可以向其他节点提供区块链的完整副本。

## **完整节点如何验证区块链的当前状态？**

完整节点从其对等节点(其他以太坊节点)下载曾经存在的每一个以太坊块，但是也检查这些块不是“假块”您的节点将通过**重放自起源块**(也称为第一块)以来的所有事务，通过检查这些块是否遵循所有共识规则，并通过确保所有事务都有效，来验证这些块是否合法。

一旦您的本地区块链验证了网络中的最新数据块，它就会被同步，现在正式成为完整节点。

从这个本地验证过程中得到的关键信息是，尽管从创世纪开始，你就从以太坊网络中的对等体那里收到了每一个块，但你不需要盲目地接受来自对等体的任何数据来达到区块链的最新状态。你通过一个“不可信”的过程到达了以太坊区块链的当前状态。

> 还念:最好的以太坊[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)

## **完整节点需要是归档节点吗？**

人们**认为**为了有一个完全验证的以太坊区块链(又名完整节点)，你需要运行一个档案以太坊节点。运行一个存档节点被认为是以太坊的一个问题，因为一个存档节点目前占用高达 1.4 太字节(数据点由奇偶技术公司的开发者 [Afri Scheoden](https://twitter.com/5chdn) 提供)。

![](img/e7ceb601c40b52f5c5cc9b0f0b11b61d.png)

source: [pixabay.com](https://pixabay.com/en/binary-random-numbers-digital-ones-1254484/)

存档节点保存每个块的所有中间/过渡/历史状态。每次添加一个块，状态数据被添加并从状态 trie 的**最新版本**中移除。尽管以太坊状态的最新版本中不断添加和删除数据，但存档节点仍然保留所有与先前状态相关的删减数据。

存档节点可以使用这些数据来查看以太坊在每个历史区块的状态。这意味着您可以看到每个公钥余额和智能合约状态在任何特定时间点(或者更准确地说是“阻塞”)的样子。你是否对 4199900 街区的所有账户都是什么样子感到好奇？存档节点将在那时保存整个全局状态。

如您所见，归档节点不仅仅是一个完整的节点，也不是必需的。我在这篇文章中的主要论点是**我们不需要运行一个存档节点来运行一个完全验证的区块链**。我们也可以运行一个带有修剪过的**状态 trie** 的完整节点！

## **完全验证区块链的修剪节点？**

![](img/41152cd0463ddd47991d8405e774e9ac.png)

奇偶以太坊客户端给你选择**完全验证和重放自起源**以来的每个事务/块，但也允许你**修剪以太坊状态尝试**以节省空间。这意味着在 [1024 阻塞](https://dev.to/5chdn/the-ethereum-blockchain-size-will-not-exceed-1tb-anytime-soon-58a)后，所有关于以太坊历史状态的数据都将被删除。

修剪状态 trie 可以节省大量磁盘空间，因为是历史状态数据造成了区块链膨胀。与归档节点占用的 1.4 相比，修剪后的区块链可以占用 90 GB(数据点由 Parity Tech 的开发人员 [Afri Scheoden](https://twitter.com/5chdn) 提供)。

尽管来自旧状态 trie 的数据被删除，但是重新创建该状态 trie 所需的所有信息仍然保存在本地区块链上。为了构建一个同样被修剪的完整以太坊节点，使用奇偶以太坊客户端的 **—快速修剪(默认为开)和—无扭曲**选项。

## **剪枝还是有得有失！**

修剪后的区块链仍然保存了更多的历史州，而不仅仅是最新的街区，因为它需要这些州，以防区块链重组。当累积难度更高的区块链版本出现并归还区块链的几个街区时，区块链就会重组。

奇偶以太坊上为修剪后的区块链保存的历史状态的默认数量是 1024，但是如果您想进一步保护自己免受链重组的影响，您也可以选择增加这个数量。

## **普通用户还能运行一个完整的节点吗？？？**

希望我让你相信以太坊的存储要求不会成为普通以太坊用户的进入障碍。

在以后的帖子中，我**可能**会探讨其他阻碍用户体验运行节点的全部好处的问题。我特别感兴趣的是比较运行一个完整节点和仅仅使用[以太扫描](http://etherscan.io)的用户体验。

感谢阅读！

## 如果你喜欢你所读的，在 [Twitter](https://twitter.com/jmartinez_43) 和 [Medium](/@julianrmartinez43) 上关注我吧！

我要特别感谢 [Afri Scheoden](https://twitter.com/5chdn) 校对我的文章。谢谢你在百忙之中抽出时间。

# 资源/引用/了解更多

[](https://dev.to/5chdn/the-ethereum-blockchain-size-will-not-exceed-1tb-anytime-soon-58a) [## 以太坊-区块链的大小不会很快超过 1TB。

### 每月一次，用户在“r/以太坊”上发布图表，预测以太坊的区块链规模将很快超过 1 TB。我…

开发到](https://dev.to/5chdn/the-ethereum-blockchain-size-will-not-exceed-1tb-anytime-soon-58a)  [## 奇偶校验文档-常见问题

### 将奇偶以太坊客户端与链顶端同步的最快方法是结合使用 Warp sync

wiki.parity.io](https://wiki.parity.io/FAQ)  [## 完整节点

### 完全节点是完全实现加密货币网络所有核心共识规则的计算机，使用…

www.investopedia.com](https://www.investopedia.com/terms/f/full-node.asp)  [## 不同种类的比特币全节点——Bitseed

### 在比特币网络中，完全节点是共识规则的执行者。完整节点(“完全验证”的缩写…

bitseed.org](https://bitseed.org/the-different-kinds-bitcoin-full-nodes/) 

## **同样，阅读**

*   [顶级 DeFi 项目](/coinmonks/defi-future-10-promising-projects-in-the-defi-world-ff2b697ab006)
*   [最佳加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   [unis WAP 最佳钱包](/coinmonks/best-wallets-to-use-uniswap-e91a6385d9e8)
*   [Bitsgap 评论](https://blog.coincodecap.com/bitsgap-review)——一个轻松赚钱的加密交易机器人
*   [Quadency Review](https://blog.coincodecap.com/quadency-review-a-crypto-trading-automation-platform) -专为专业人士打造的加密交易机器人
*   Bitmex 的[保证金交易指南](/coinmonks/the-idiots-guide-to-margin-trading-on-bitmex-dbbd7742c6fc?source=friends_link&sk=7bfa99d2a181142510c8442c8ddb0786)
*   [加密摇摆交易权威指南](/coinmonks/the-definitive-guide-to-crypto-swing-trading-7e4af6496d4d?source=friends_link&sk=70448050bd9323b42f63bfc0bb1e60d1)
*   [Bitmex 高级保证金交易指南](/coinmonks/bitmex-advanced-margin-trading-guide-2270c195ce25?source=friends_link&sk=1d986cca731f5084b9a2db4a4bc4a7ad)
*   顶级[比特币节点](https://blog.coincodecap.com/bitcoin-node-solutions)提供商
*   [开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com?utm_source=coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://coincodecap.com?utm_source=coinmonks)