# 比特币的基础知识及其工作原理

> 原文：<https://medium.com/coinmonks/basics-of-bitcoin-and-how-it-works-148328dfa1ca?source=collection_archive---------10----------------------->

比特币是一种加密货币，这意味着它是一种使用加密技术来实现操作和安全的货币形式。它创建于 2009 年，就在 2008 年金融危机之后。

现在，在我们试图理解比特币的工作原理之前，让我们先来看看它为什么会被创造出来。

## 比特币为什么被创造出来？

2008 年 1 月，华尔街公司雷曼兄弟公布了 2007 财年的财务报表。他们公布了创纪录的 590 亿美元收入和创纪录的 42 亿美元收益。九个月后，他们倒闭了。那是怎么发生的？

很明显，他们在篡改委托他们保管的账簿。当报告季节到来时，他们正在把债务从账本上移走，一旦报告季节结束，他们就把债务还原。这严重违背了顾客对它的信任。这就提出了一个问题，即是否真的可以信任银行来维护账本。如果没有，有没有其他选择？

这个不得不信任银行的问题自银行成为总账管理人以来就一直存在。15 世纪末，出现了一种新的簿记形式，称为复式簿记(这也是现代会计的基础)。这使得交易只是由可信的第三方(即银行)维护的分类账中的条目，并消除了实际交换硬币或代币的需要(这在远距离上很难做到)。这有助于大大扩展人类交流的能力，并建立了现代世界。但是这也带来了一个新的问题，那就是必须信任记录保存者。

这种信任问题正是比特币试图解决的。它通过将维护交易分类账的责任委托给用户群体而不是单个实体(如银行)来实现这一点。这个用户社区检查彼此的工作，并同意共同记录真相(共识)。

为了做到这一点，它重新设想了账本本身。分类账不再是单一的交易记录，而是分散的计算机网络中的集体记录，每个计算机都维护自己版本的分类账。大多数人同意的版本被认为是真理。这种新形式的分类账被称为区块链。

## 什么是区块链？

区块链实际上是一串积木。块由一组事务和一些其他信息组成，如下图所示。

![](img/383db8821a3abb47c5a79780b9e983cc.png)

Bitcoin blockchain structure

一个块的大小(即块大小)是 1Mb。

它始于区块链的第一个地块——创世纪地块。每个后续块包含前一个块的[哈希](https://www.investopedia.com/terms/h/hash.asp)(Prev _ hash)。在计算它自己的散列之前，前一个块已经包括了它的前一个块的散列。因此，它形成了一连串的方块。对块的任何改变将导致其散列值的改变，该散列值将不再匹配下一个块的 Prev_hash 字段的值，从而打破该链。

**Tx_root** 是需要包含在块中的所有事务的哈希树(或 [merkle 树](https://en.wikipedia.org/wiki/Merkle_tree))的根。miner 还在块中包含了一种称为 coinbase 的特殊事务(稍后将详细介绍)。**时间戳**是创建该块的时间。

**Nonce** 是一个数字，使得包括它之后的块的散列值低于基于当时网络的难度值计算的目标值(阅读 [this](https://bitcoin.stackexchange.com/questions/44579/how-is-a-block-header-hash-compared-to-the-target-bits) 了解更多信息)。目标值越低，低于该值的有效哈希值集就越小，并且越难找到满足条件的随机数。

网络的难度值会增加或减少，以保持创建新区块所需的时间(区块时间)接近 10 分钟。如果网络中的矿工数量(或可用计算能力)增加，难度也会增加，反之亦然。

## 交易的生命周期

![](img/6aa1812d8f0823aa6a4dab48deab098f.png)

Lifecycle of a transaction

让我们来看看当一些金额从发送者的钱包发送到接收者的钱包时会发生什么:

1.  发送者从他们的比特币钱包发起交易，接收者的钱包地址作为接收者。
2.  钱包验证交易(检查金额是否超过余额，接收方地址是否有效等)并将其广播到网络。
3.  miner 节点接收事务，并将其添加到它们的内存池中。(内存池是 miner 节点上的内存空间，挂起的事务在被添加到块之前存储在这里)
4.  挖掘者试图解决这个难题(如下一节所述),试图找到一个有效的 nonce 值。谁先解决这个难题，谁就能创造下一个积木。这就叫 [**工作证明(PoW)**](https://www.investopedia.com/terms/p/proof-work.asp) **。**
5.  成功的 miner 节点将创建一个新的块，从 mempool 中挑选事务，并将它们添加到 merkle 树中，并设置适当的块头(如前一节所述)。
6.  矿工将包括块到他们的本地版本的区块链，也广播到网络。其他节点将接收该数据块，对其进行验证，并将其添加到区块链的本地版本中。
7.  一旦该块被网络的大多数参与者添加，它就不能被移除。现在，可以认为该区块已成功添加到区块链。
8.  汇款金额将反映在收款人的钱包中。

## 采矿是如何工作的？

网络中的每个矿工维护他们自己的本地版本的区块链。此外，它们将未决事务(即，尚未添加到区块链的事务)存储在它们的内存池中。

为了给区块链增加下一个区块，矿工们开始尝试解决一个难题。难题是找到一个随机数值，使得在包括它之后的块的散列值低于目标值。因为即使很小的 nonce 也会导致完全不同的哈希值，所以没有模式可以用来以更有效的方式找到 nonce。找到随机数的唯一方法是一个接一个地尝试所有选项的强力方法。这需要大量的计算能力。

第一个解决这个难题的矿工被允许(通过网络)创建下一个块。这叫做 [**【战功】**](https://www.investopedia.com/terms/p/proof-work.asp) 。挖掘器从它们的内存池中挑选要包含在下一个块中的事务。被挑选的交易是根据矿工的判断。采矿者可以选择仅添加那些包括高于某个阈值的交易费用的交易。因此，即使交易费在技术上是可选的，最好还是包括在内。否则，交易可能会被延迟或拒绝。

除了交易费之外，矿工们还会收到新创造的比特币的预定奖励。目前的奖励是每块 6.25 新 BTC。每创作 201，000 个积木或大约每 4 年，奖励金额减半。为了获得这一奖励，块中包含一个称为 coinbase 的特殊交易，以矿工的钱包地址作为收款人。一旦这个区块被添加到区块链，区块奖励反映在矿工的钱包。**现存的所有比特币都是由这种交易创造的。**

## 参考

1.  真相机器:区块链和万物的未来(书)
2.  [https://en.wikipedia.org/wiki/Bitcoin](https://en.wikipedia.org/wiki/Bitcoin)
3.  [https://dev . to/gmfcastro/the-bit coins-life cycle-overview-1 fld](https://dev.to/gmfcastro/the-bitcoins-lifecycle-overview-1fld)
4.  https://www.investopedia.com/terms/b/bitcoin-mining.asp
5.  【https://www.investopedia.com/terms/p/proof-work.asp 
6.  [https://bit coin . stack exchange . com/questions/32230/are-bit coin-fees-optional](https://bitcoin.stackexchange.com/questions/32230/are-bitcoin-fees-optional)
7.  [https://bit coin . stack exchange . com/questions/85896/mining-difficult-and-leading-zeros](https://bitcoin.stackexchange.com/questions/85896/mining-difficulty-and-leading-zeros)
8.  [https://bit coin . stack exchange . com/questions/44579/how-a-block-header-hash-comparated-to-the-target-bits](https://bitcoin.stackexchange.com/questions/44579/how-is-a-block-header-hash-compared-to-the-target-bits)

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 也阅读

[](/coinmonks/leveraged-token-3f5257808b22) [## 杠杆代币[多头代币]终极指南

### 杠杆化令牌是具有杠杆化风险敞口的 ERC20 令牌，不考虑保证金、要求、管理…

medium.com](/coinmonks/leveraged-token-3f5257808b22) [](https://blog.coincodecap.com/crypto-exchange) [## 最佳加密交易所| 2021 年十大加密货币交易所

### 编辑描述

blog.coincodecap.com](https://blog.coincodecap.com/crypto-exchange) [](https://blog.coincodecap.com/best-swap-platforms) [## 2021 年最佳加密交换平台| CoinCodeCap

### 编辑描述

blog.coincodecap.com](https://blog.coincodecap.com/best-swap-platforms)