# 使用 NOWNodes 创建 Dogecoin wallet 的分步指南

> 原文：<https://medium.com/coinmonks/a-step-by-step-guide-to-creating-dogecoin-wallet-with-nownodes-b3ba37428b2e?source=collection_archive---------0----------------------->

![](img/a3008ba472924611d6db2a9df6d2cb9c.png)

我们用户的故事。我们知道运行一个节点可能会令人头痛，所以我们帮助开发团队更好地关注新功能和 dapp—[并且我们会做剩下的事情](https://nownodes.io/)。

# 我有一个梦想——它叫 DOGE

我是一名 PHP 开发人员，今天我将告诉你我创建 Dogecoin [Crypto Wallet](https://coincodecap.com/category/wallets) (以下简称 DOGE)的步骤和遇到的问题。从技术上来说，有必要开发一个网站，用户可以为自己创建钱包，接收，存储和发送 DOGE。

看起来最简单和最相关的解决方案是购买一个专用服务器，在其上运行 Dogecoin nodes (node ),并通过 RPC 本地连接到它。但是这个选择最初没有成功，原因有二。

首先，由于服务器的成本，具有必要参数的服务器每月大约花费 50 美元。这个项目最初并不打算商业化。它被设计成对用户免费，所以它的维护成本应该是最低的。

其次，节点支持需要一个合格的这方面的专家，这也是一个额外的成本。我们尝试使用我们自己的资源来提升 Dogecoin 节点，但是它非常不稳定——它经常挂断或者看起来与主网络不同步，这当然让我们感到沮丧。

# 如何用远程节点制作东西

要使用外部节点，必须回答三个问题:

1)如何创建地址以及在哪里存储密钥？

2)如何接收新交易？

3)如何产生新的交易？

第一个问题没有这么简单。在阅读了大量文章并检查了数兆字节的代码(不仅仅是 PHP)后，编译了一组库，使得生成密钥对和地址成为可能。

第二个问题最简单。要找出哪个事务被确认，您需要查看新的块。如何接收新块？利用区块链的“慢度”，你可以周期性地询问一个节点(甚至一分钟一次)。如果出现新的块，就向节点请求它的内容。即使交易在 2 分钟后收到，也没有人会抱怨，并且考虑到需要多次确认，这通常不会被察觉。

第三个问题的解决需要大量的时间和努力。没有现成的解决办法。交易形成被多次描述，但是没有例子。那些存在的例子只包含最简单的选择。Dogecoin 建立在比特币的原理上，这很好。大多数信息都是从那里获得的，事实证明，最优交易的形成是一个复杂的过程。为了获得低佣金，交易长度应该尽可能短，因此，我们需要一种算法，从十几个输出中选择那些将给出最小“变化”的输出，并且这种变化不能少于灰尘！调试和测试这个算法花了几个月的时间。

# 对可用选项的简短研究

现在回到连接到[区块链](https://blog.coincodecap.com/what-is-blockchain-a-simple-guide-for-dummies/)的问题。

第一个实现选项是节点租赁。搜索返回了几个选项，但所有的选项要么是非 Dogecoin，要么每月花费数百美元。

第二种选择是通过 API 块浏览器进行连接。对“Dogecoin blockexplorer API”的搜索返回了以下选项:

[Sochain.com:](https://www.sochain.com/DOGE:) 和 [chain.so:](https://chain.so/DOGE/:) —这是来自 Block.io，Inc .的相同资源，有一个 [API](https://sochain.com/api) ，但它只能在浏览器中工作！站点保护不会错过来自脚本的调用。

木椅是一种强大的资源。缺点——免费获得 API 密钥的困难和付费密钥的高成本。

[doge . token view . com:](https://doge.tokenview.com/:)500 内部服务器错误

[Cryptoapis.io:](https://cryptoapis.io/:) 每天 500 个调用的限制

[Live.blockcypher.com/doge](https://live.blockcypher.com/doge/)—巨大的资源！原始交易签名技术。缺点:对每分钟 API 调用次数的限制，节点 desync(通过它们发送的事务对其他节点不可见)。我们在这里做的最初研究。

[Dogeblocks:](https://dogeblocks.com/:) Insight 最初是带着它的 API 来的——一个很棒的免费资源！我们用了很长时间。但后来它“死了”，几个月都没有工作。而且现在有个 Blockbook，不能一次给几个地址 UTXO。

[Dogechain.info:](https://dogechain.info/:) —也是来自 Block.io，Inc .的资源免费，但有限制 [API](https://dogechain.info/api/blockchain_api) :没有“get block transactions”，也没有“get UTXO at multiple addresses”。有一段时间，它是网络上唯一一个我们可以和 Dogecoin“交流”的资源。为了获得块事务，我甚至必须解析 HTML 页面。

一切都不稳定，因此，有一次，一个用户建议资源[现在节点](http://nownodes.io/)。该资源提供租赁节点。大部分都安装了 Blackbook API。我们联系了代表，并得到了一把钥匙。从那时起，我们一直与他们合作。

# 结论

简而言之:为了创建 DOGE web 钱包，至少需要以下 API:

1)获得网络中的最后(最大)块号

2)获取该块的所有事务的内容/数据(不仅仅是 txid)

3)获得一个地址和多个地址的平衡(在 Blockbook 中不可用)

4)获取一个地址和几个地址的 UTXO 列表(在 Blockbook 中不可用)

5)将生成并签名的交易发送到网络

事实证明，仅仅向网络发送事务是不够的！即使节点返回了肯定的响应(没有错误)，这也不意味着事务已经进入了 mempool。发送一段时间后，有必要检查“交易是否存在？”有时候它就这么消失了！我们仍然不明白这背后的原因。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)

NOWNodes —资源管理器和节点 API。

访问[我们的网站](http://nownodes.io)并在 1 天内访问您的节点。

> 另读:[最佳比特币硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)