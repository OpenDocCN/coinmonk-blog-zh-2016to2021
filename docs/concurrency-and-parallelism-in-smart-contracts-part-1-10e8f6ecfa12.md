# 智能契约中的并发性和并行性，第 1 部分

> 原文：<https://medium.com/coinmonks/concurrency-and-parallelism-in-smart-contracts-part-1-10e8f6ecfa12?source=collection_archive---------1----------------------->

## 为什么以太坊的吞吐量如此之低

以太坊很慢——慢得令人难以忍受。我最近做了一个简单的 USDC 批准交易，大约花了 3 个小时来验证。这里有一个更有启发性的统计:以太坊平均每 10~20 秒发布一个区块。每个块包含不到 350 个事务。所有这些大致相当于每秒 30 次交易 (tps)。批评以太坊慢的时候，经常提起 Visa 的 2,000 tps。也许这是一个不公平的比较，因为以太坊仍然处于发展阶段。然而，以太坊似乎不太可能很快主宰数字金融。

以太坊的低吞吐量是一个根本性的问题。以太坊是一个**基于账户的区块链**:账本状态被定义为从账户地址到一段数据的键值映射。简单的 ETH 交易(**价值转移**)增加和减少账户对的 ETH 余额数据。类似地，一个更复杂的交易(**契约调用**)会使指定账户的数据发生变异。在这种情况下，以太坊交易是全球分类账状态的过渡功能。这是什么使得**以太坊虚拟机** (EVM)图灵完整，并允许智能合约成为可能；以太坊智能合约本质上是一个可交互的账户数据。

现在让我们看看 EVM 是如何处理或验证这些事务的。请注意，并行处理所有事务是不合理的。按照设计，所有事务都试图改变整个全局状态。如果事务并行运行，EVM 将偏向于**竞争条件**:两个程序(在本例中是事务)试图并行递增一个`uint`变量。因为两个程序同时访问变量，所以变量只增加一次，而不是两次。为了解决这种并发错误，以太坊选择逐个处理事务。换句话说，EVM 是一个单线程的状态机。结果以太坊实现了**并发**，却没有实现**并行**。

以太坊类似于只有一个处理时间不一致的出纳员的票队列。排队的人是等待验证的交易，孤独的出纳员是虚拟机。当我们把汽油费计算在内时，事情就变得更复杂了。现在，任何人都可以额外付费插队。异常长的队伍意味着那些在排队中买不起更好位置的人将不得不等待过长的时间来处理他们的票。

以太坊的低吞吐量是有问题的，尤其是从 web3.0 的角度来看。比方说以太坊确实成为了所有网络应用的媒介。如果其目前的吞吐量持续下去，像投票赞成 Reddit 帖子这样的琐碎任务可能需要两个多小时才能处理完；这不是区块链对未来最现实的展望，但它证明了我的观点。我们生活在一个速度决定一切的时代，以太坊太慢了。

你可能会问，为什么不选择性地应用并发性呢？详细地说，为什么不将并发性应用于冲突的事务—例如，将值转移到同一个帐户—并并行处理其余的事务。不幸的是，Saraph 和 Herlihy (2019，p2)向我们展示了所实现的速度提升充其量是适度的。

许多加速以太坊和提高其可扩展性的解决方案已经被提出。最近的 EIP-1559——伦敦硬分叉——不会直接影响以太坊的交易速度，但理论上应该会通过减少普通用户在交易处理前必须等待的潜在块数，来稳定其在大规模交易高峰时的波动性。然后是**第二层的累积**，这应该会直接影响以太坊的吞吐量，而不会危及太多区块链宝贵的不信任。

与此同时，其他模拟区块链的通用虚拟机也在积极开发中。有些已经成功实现了并行，承诺吞吐量远高于以太坊的 30 tps。我想特别关注阿尔格兰德、索拉纳和卡尔达诺，以及他们在智能合约中实现并行的独特方法。

参考

*   [Saraph，V & Herlihy，M，2019，“以太坊智能合约中投机并发性的实证研究”](https://drops.dagstuhl.de/opus/volltexte/2020/11968/pdf/OASIcs-Tokenomics-2019-4.pdf)
*   [布特林，V，“EIP 1559 常见问题解答”](https://notes.ethereum.org/@vbuterin/eip-1559-faq#How-would-a-spike-of-high-usage-look-like-under-EIP-1559-compared-to-the-status-quo)

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 另外，阅读

*   [网格交易机器人](https://blog.coincodecap.com/grid-trading) | [Cryptohopper 审查](/coinmonks/cryptohopper-review-a388ff5bae88) | [Bexplus 审查](https://blog.coincodecap.com/bexplus-review)
*   [7 个最佳零费用加密交换平台](https://blog.coincodecap.com/zero-fee-crypto-exchanges)
*   [去中心化交易所](https://blog.coincodecap.com/what-are-decentralized-exchanges) | [比特恩斯 FIP](https://blog.coincodecap.com/bitbns-fip) | [Pionex 审核](https://blog.coincodecap.com/pionex-review-exchange-with-crypto-trading-bot)
*   [用信用卡购买密码的 10 个最佳地点](https://blog.coincodecap.com/buy-crypto-with-credit-card)
*   [3 commas vs . Pionex vs . crypto hopper](https://blog.coincodecap.com/3commas-vs-pionex-vs-cryptohopper)
*   [加密复制交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [如何在 WazirX 上购买比特币](/coinmonks/buy-bitcoin-on-wazirx-2d12b7989af1)
*   [CoinLoan 点评](https://blog.coincodecap.com/coinloan-review)|[Crypto.com 点评](/coinmonks/crypto-com-review-f143dca1f74c) | [火币保证金交易](/coinmonks/huobi-margin-trading-b3b06cdc1519)
*   [Bookmap 评论](https://blog.coincodecap.com/bookmap-review-2021-best-trading-software) | [美国 5 大最佳加密交易所](https://blog.coincodecap.com/crypto-exchange-usa)
*   最佳加密[硬件钱包](/coinmonks/hardware-wallets-dfa1211730c6) | [Bitbns 评论](/coinmonks/bitbns-review-38256a07e161)
*   [新加坡十大最佳加密交易所](https://blog.coincodecap.com/crypto-exchange-in-singapore) | [购买 AXS](https://blog.coincodecap.com/buy-axs-token)
*   [投资印度的最佳加密软件](https://blog.coincodecap.com/best-crypto-to-invest-in-india-in-2021) | [WazirX P2P](https://blog.coincodecap.com/wazirx-p2p)
*   [加拿大最佳加密交易机器人](https://blog.coincodecap.com/5-best-crypto-trading-bots-in-canada) | [库币评论](https://blog.coincodecap.com/kucoin-review)
*   [火币的加密交易信号](https://blog.coincodecap.com/huobi-crypto-trading-signals) | [HitBTC 审核](/coinmonks/hitbtc-review-c5143c5d53c2)
*   [如何在 FTX 交易所交易期货](https://blog.coincodecap.com/ftx-futures-trading) | [OKEx vs 币安](https://blog.coincodecap.com/okex-vs-binance)