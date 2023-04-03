# 原子互换:分散贸易

> 原文：<https://medium.com/coinmonks/atomic-swaps-descentralized-trade-5d30b4980f3?source=collection_archive---------7----------------------->

通常，当我们想要在不同的加密货币之间进行交易时，我们会使用交易所或 p2p 交易(有或没有托管)。在这两种选择中，我们都必须**信任中介(交换/托管)或另一方**。

# 不要相信，验证！

比特币引入的伟大创新是一个没有可信第三方的金融系统的生存能力，也就是一个不可信的系统。

利用这一原理，原子互换(Atomic Swap)应运而生，这一解决方案使得在没有第三方的情况下交换不同的加密货币成为可能。在下面的部分中，我将解释它是如何工作的，并给出一些实现的例子:

# 要求

两种硬币都应支持智能合约，该合约允许以下方面:

1.  时间验证(TimeLock) —例如:资金只能在 1 周后使用；
2.  哈希函数验证(HashLock) —示例:只有当发现质询问题(通过哈希“隐藏”)时，才能花费资金；
3.  可见哈希输入(公共前映像)—示例:可以通过查看事务数据来查看质询答案。

具有 1 个和 2 个验证的合同被称为 HTLC(散列时间锁合同),也用于 Lightning 网络协议。

# 它是如何工作的

让我们来看看爱丽丝和鲍勃之间成功的 1 BTC 对 10 埃特的交易。

1.  Alice 生成一个随机值(secret)，执行一个哈希函数，获得 hashedSecret
2.  爱丽丝转移 1 BTC 到智能合同。为了赎回基金，以下条件需要为真:
    A)交易必须包含秘密值；
    B)收件人必须是鲍勃的 BTC 地址；
    C)交易必须在合同创建后 48 小时内完成。
3.  Bob 将 10 个 eth 转移到智能合约。契约类似于 Alice 创建的:
    A)交易必须包含秘密值；
    B)收件人必须是爱丽丝的 ETH 地址；
    C)交易必须在合同创建的 **24 小时内**完成。
4.  此时，Alice 和 Bob 的资金受合同控制，只有在知道秘密值的情况下才能使用。
    Alice 是如何产生这个价值的，她能够将 Bob 发送的 10 个 eth 兑换到她的钱包中。
5.  通过分析 Alice 的最后一次交易，Bob 能够知道秘密值(公开的前映像),并且能够访问 1 BTC 并完成交易。

# 如果出了什么问题呢？

前面的例子是基于一次成功的操作。可能会出现一些问题，解决方案就是为了处理这些问题而设计的。

1.  如果 Bob 没有创建他的合同，Alice 会冻结他的 1 BTC 吗？不可以，为了简化上面的解释，合同被省略的另一个条件是:合同创建 48 小时后，爱丽丝可以退款。Bob 是 48 小时内资金的唯一接收人，在此之后，只有 Alice 可以接收资金。
2.  鲍勃也一样，如果爱丽丝没有通过赎回 10 个 eth 来揭示秘密的价值，他可以在 24 小时后退款。
3.  时间锁是不同的(48 小时和 24 小时),因为否则爱丽丝有可能在截止日期结束时赎回 eth，并且如果鲍勃没有迅速赎回，仍然有可能退还她的 1 BTC。

# 履行

[在此链接](https://swapready.net/)，可以检查某些脚本代码与原子交换要求的兼容性。

## 比特币

比特币的智能合约使用图灵不完整脚本语言，下面的代码基于 Decred 的资源库中的[实现。](https://github.com/decred/atomicswap)

```
OP_IF OP_SHA256 **<HASHED_SECRET>** OP_EQUALVERIFY OP_DUP OP_HASH160 **<COUNTER_PARTY_PUBKEY>** OP_ELSE **<LOCK_TIME>** OP_CHECKLOCKTIMEVERIFY OP_DROP OP_DUP OP_HASH160 **<MY_PUBKEY>** OP_ENDIF OP_EQUALVERIFY OP_CHECKSIG
```

## 以太坊

下面是使用 Solidity 语言的一个可能的契约实现([基于这个实现](https://github.com/AltCoinExchange/ethatomicswap))

# 骗局

1.  **Privacy** :跟踪不同区块链之间交换的值并不困难，因为相同的 hashedSecret 在两个平台上都是可见的。
2.  可用性:仍然有一些工具/钱包支持这项技术。
3.  **速度**:与集中式替代方案相比，运行速度较慢。
4.  **兼容性**:并非所有的加密币都兼容。

# 结论

已经有倡议使用第二层解决方案(如闪电网络)来执行链外交换，解决问题 1 和 3。

原子互换是走向贸易操作去中心化的重要一步。它目前是大额交易的一个很好的解决方案，但是仍然需要更多的工具和技术的开发来实现大规模的采用。

# 进一步阅读

*   [https://coin telegraph . com/news/decentralized-exchange-off-chain-atomic-swaps-and-a-brief-look-to-the-future](https://cointelegraph.com/news/decentralized-exchanges-off-chain-atomic-swaps-and-a-brief-look-into-the-future)
*   [https://seg wit . org/my-vision-for-seg wit-and-lightning-networks-on-lite coin-bit coin-cf 95 a 7 ab 656 b](https://segwit.org/my-vision-for-segwit-and-lightning-networks-on-litecoin-and-bitcoin-cf95a7ab656b)