# 完全了解用户证据:无需支付天然气费用即可使用储物空间

> 原文：<https://medium.com/coinmonks/full-knowledge-user-proofs-working-with-storage-without-paying-for-gas-e124cef0c078?source=collection_archive---------3----------------------->

> [问题陈述](#59a0)
> [标准方法](#54f3)
> [Let ' s get schwifty](#a0d4)
> [局限性](#1d0b)
> [加成 1:更便宜的哈希计算](#b489)
> [结论](#9d37)
> [鸣谢](#db08)

[收益导师计划](https://github.com/alcueca/solidity-mentorship/issues)提供给你解决的问题之一是[建立一个多抵押品的金库](https://github.com/alcueca/solidity-mentorship/issues/6)。它漫不经心地提到了燃气效率，但没有设定任何目标。自然，我必须看看我能把汽油用量推到多低。

# 问题陈述

这是它的一个简化版本:一个(单用户)金库，用户可以从“祝福”列表中存入令牌作为抵押，并以此为抵押借用戴。

让我们尝试优化`borrow()`气体效率！为了让事情变得有趣，我们将假设用户在金库中添加了 100 种不同的抵押品类型。

# 标准方法

考虑到气体效率的标准方法可能是这样的(不考虑访问控制和多用户支持):

`borrow()`简单地遍历用户存放的所有抵押品，将它们的价格相加，并允许用户借入该金额。

我们负担不起为用户的每笔存款调用外部 oracle([每次调用至少使用 2600 gas](https://eips.ethereum.org/EIPS/eip-2929#abstract) )，所以我们将资产价格存储在`Assets`结构中，并有一个触发另一个契约的链外流程，该契约每 10 分钟调用一次 Oracle，然后每 10 分钟调用一次`setAssetPrices()`。**无外线来电** `**borrow()**` **- >降低用户燃气费用。** 

总的来说，每次调用 borrow()花费我们 100 项资产约 60 万美元，1 项资产约 5.3 万美元。

# 我们去睡觉吧

访问契约存储是以太坊中开销最大的操作之一。在我们的`borrow()`函数中我们做了很多:至少 100 次获取存款余额/资产 ID，另外 100 次获取资产价格。每一次存储[读取花费我们 2100](https://eips.ethereum.org/EIPS/eip-2929#abstract) ，所以那是 2100 * 200 = 400k 汽油。我们可以压缩一下`Asset`和`Deposit`结构，也许可以减少一半，但是 20 万还是很多。

幸运的是，`borrow()`根本不用访问存储。如果`assets`和`deposits`在[调用数据](https://docs.soliditylang.org/en/latest/types.html#data-location)中可用，我们就可以从那里读取它们。访问呼叫数据只需要 3 块钱，不会比这更低了。

实现起来很简单，`borrow()`看起来和初始版本几乎一样:

客户端:

我们只是简单地要求用户给我们函数运行所需的全部数据。

现在，有一个明显的问题:用户可以对他们的存款撒谎，我们不能盲目信任他们。我们需要用户向我们提供**证明**证明`_deposits`和`_assets`与`deposits`和`assets`相同。有一种简单的方法可以做到这一点:比较存储值和用户提供值的哈希值

**全知识用户证明(FKUP)** 将`borrow()`100 项资产的用气量从约 600k 降至 285k，1 项资产的用气量从 53k 降至 52k。

# 限制

FKUP 不是灵丹妙药，有一点局限性:

*   如果你的合同的用户是另一个合同，汽油费就没了。除非他们采用相同的模式，使`Asset[] calldata _assets, Deposit[] calldata _deposit`成为他们接口的一部分，并简单地将数据传递给你
*   如果您的功能不仅仅是处理存储中的一些数字(想象一下`borrow()`调用外部 oracle 获取价格)，那么汽油节省仍然会存在，但它们会被外部调用的成本所掩盖
*   每次`_assets`或`_deposit`改变时，它们的散列都需要重新计算。如果你的数据经常变化，`borrow()`仍然很节能，但是`deposit()`和`setAssetPrices()`可能会抵消这些节省。
*   如果数据经常变化，用户数据有可能与最近的合同数据不匹配。如果不希望这样，在散列不匹配的情况下，`borrow()`可以回退到从存储中读取，而不是恢复。

# 好处 1:更便宜的哈希计算

```
function x(uint256 amount, Asset[] calldata _assets) public { 
   require(keccak256(abi.encode(_assets)) == assetsChecksum, "assets checksum");
}
```

这里不需要 abi.encode()调用。为什么？_assets 位于 calldata 中，并且已经在客户端用 abi.encode()序列化了。唯一的挑战是 keccak256 不能从 calldata 中读取，所以我们先用一点汇编把 _assets 复制到内存中。总的来说，我们又节省了 5 万吨汽油:

234k 天然气和 100 项资产，51k 天然气和 1 项资产。

有一种方法可以把总的用气量降低到 150k，我会把它留到下一篇文章。

# 结论

我们测试了 3 个版本的`borrow()`函数:

1.  香草一号，很好理解，差不多用 **600k 气**
2.  香草 FKUP:还是容易理解，用 **285k 气体**
3.  轻度优化的 FKUP:有点难以理解，但仍然可以， **234k 气体**

Vanilla FKUP 可能是最划算的，因为它不使用 Yul，可以节省 2 倍的汽油。

由于[的限制](#1d0b)，这不是一个放之四海而皆准的模式，但它可能是方便的。

下一篇文章将在 Solidity 和 Yul 的混合中增加一个版本，并详细介绍 dapptools 和 C++的比较。

# 感谢

非常感谢[智能合同指导协议](https://twitter.com/yield?s=20)和[阿尔贝托](https://twitter.com/alcueca)是一位令人敬畏的导师。如果你对成为导师或学员感兴趣，请查看[以太导航](https://ethernautdao.medium.com/introducing-the-ethernautdao-21bfca20ee80)。
[devtooligan](https://twitter.com/devtooligan) 帮助讨论了这个想法的早期版本并编辑了这篇文章。

## 也阅读

[](https://blog.coincodecap.com/crypto-exchange) [## 最佳加密交易所| 2021 年十大加密货币交易所

### ICON _ PLACEHOLDEREstimated 预计阅读时间:28 分钟加密货币交易所的加密交易需要知识…

blog.coincodecap.com](https://blog.coincodecap.com/crypto-exchange) [](https://blog.coincodecap.com/crypto-lending) [## 2021 年 10 大最佳加密贷款平台| CoinCodeCap

### 当谈到加密货币贷款时，大量因素等同于良好的收入状况。此外，借款的一部分…

blog.coincodecap.com](https://blog.coincodecap.com/crypto-lending) [](/coinmonks/crypto-trading-bot-c2ffce8acb2a) [## 2021 年最佳免费加密交易机器人

### 2021 年币安、比特币基地、库币和其他密码交易所的最佳密码交易机器人。四进制，位间隙…

medium.com](/coinmonks/crypto-trading-bot-c2ffce8acb2a) [](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b) [## 最佳 4 个加密交易信号电报通道

### 这是乏味的找到正确的加密交易信号提供商。因此，在本文中，我们将讨论最好的…

medium.com](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b) [](https://blog.coincodecap.com/blockfi-review) [## BlockFi 评论 2021:利弊和利率| CoinCodeCap

### 今天，我们提出了一个全面的 BlockFi 评论，这是一个成立于 2017 年的加密贷款平台，拥有其…

blog.coincodecap.com](https://blog.coincodecap.com/blockfi-review) [](/coinmonks/buy-bitcoin-in-india-feb50ddfef94) [## 如何在印度购买比特币？2021 年购买比特币的 7 款最佳应用[手机版]

### 如何使用移动应用程序购买比特币印度

medium.com](/coinmonks/buy-bitcoin-in-india-feb50ddfef94) [](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b) [## 加密税务软件——五大最佳比特币税务计算器[2021]

### 不管你是刚接触加密还是已经在这个领域呆了一段时间，你都需要交税。

medium.com](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b) [](https://blog.coincodecap.com/best-hardware-wallet-bitcoin) [## 存储比特币的最佳加密硬件钱包[2021] | CoinCodeCap

### 保管您的数字资产很容易，但找到正确的存储方式却是一项繁琐的任务。在线钱包有一个风险…

blog.coincodecap.com](https://blog.coincodecap.com/best-hardware-wallet-bitcoin)