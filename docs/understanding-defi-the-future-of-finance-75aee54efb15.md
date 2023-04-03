# 理解定义——金融的未来

> 原文：<https://medium.com/coinmonks/understanding-defi-the-future-of-finance-75aee54efb15?source=collection_archive---------2----------------------->

## 区块链上的金融

![](img/26befd37cd011fb9d20cefbba0f405d0.png)

Photo by [Etienne Martin](https://unsplash.com/@etiennemartin?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

随着技术的发展，金融业在过去的几年里呈指数级增长。分散金融(DeFi)是一种新的金融系统，旨在实现无信任、对所有人开放、无许可，并且不需要像银行这样的中间人。为了实现这一点，DeFi 使用了区块链技术和智能合约。目前，由于以太坊区块链(及其第 2 层链)的高安全性、健壮的编程语言和强大的开发者生态系统，大多数 DeFi 项目都建立在其上。

怎么开始的？

首批 DeFi 项目之一是 MakerDAO，成立于 2015 年。它允许用户锁定 ETH 作为抵押品，并生成 DAI。戴是一种稳定的货币，在算法上与美元挂钩，因此 1 戴总是等于 1 美元。戴还可以在 MakerDAO 的 oasis 平台上用于储蓄。这再现了传统金融借出和借入资产的能力。

# DeFi 如何优于传统金融(TradFi)或集中金融(CeFi)？

传统金融包括你在日常生活中遇到的传统银行，而集中金融包括任何通过单一交换系统发送订单的系统。

TradFi 的一些例子——美国银行、富国银行、摩根大通

CeFi 的一些例子——比特币基地、Nexo、BlockFi、Celcius

DeFi-ave、Compound、Uniswap 的一些例子

与银行不同，DeFi 协议是无权限、安全和匿名的。任何有足够抵押品的人都可以用 DeFi 贷款。虽然银行根据他们的议程规定了贷款和储蓄的利息百分比，但 DeFi 协议根据供求关系提供利息百分比。

在大多数经济不稳定的国家，银行经常因为亏损而关闭，带走了用户的资金。传统银行和 CeFi 公司都遭受黑客攻击、不良贷款、监守自盗和欺诈。

写这篇博客时，最受欢迎的 DeFi 协议是 AAVE 和 Compound，在 AAVE 锁定的总价值(TVL)超过 268 亿美元，在 TVL 锁定的总价值超过 190 亿美元。

# DeFi 是如何工作的？

DeFi 的主要工作是为特定的代币创造货币市场，如 ETH、DAI、、、LINK 等。用户向这些货币市场提供代币，并从他们的储蓄中赚取利息。

当用户存入代币时，他们会收到一个替代代币，该代币相当于所提供的代币+利息。例如，当存放 ETH 时，化合物提供 CETH 代币。CETH 代币就像任何其他 ERC20 代币一样，可以在不同的钱包之间转移。CETH 被要求收回锁定在货币市场上的基础 ETH。ETH 和 CETH 代币之间的汇率将随着每一个街区而不断增加。因此，当你以后把你的 CETH 换成 ETH 时，你会得到全部的储蓄 ETH 和利息。

要从 DeFi 协议中借钱，必须提供抵押品。目前，所有贷款都是超额抵押的。借款人必须提供价值超过实际贷款的代币。每一个作为抵押品存入的代币都有一个抵押品因素，决定你能借多少。例如，ETH 和 DAI 在化合物上有 75%的附属因子。这意味着，每抵押一个 ETH，你可以借 0.75 个 ETH。

现在最大的问题是，为什么有人会贷款，当他们可以清算他们的资金并得到钱的时候？人们通常通过贷款来避免/延迟支付他们的资本收益税，或者他们不想出售他们的代币。可以借多少是有限制的。这个限度取决于货币市场的资金量和你抵押品的质量。

借入资金应始终小于抵押品乘以抵押品系数。

> 借出的金额< Collateral * Collateral Factor

If this condition fails, the collateral will be automatically liquidated and the protocol will repay the difference to the borrower.

The interest rates for lenders and borrowers are determined by the ratio between supply and the borrowed tokens in a particular market. The interests are calculated in every block, thus it is always a variable. Protocols like AAVE offers a stable APY in the short term but changes over the long term based on supply and demand.

# Risks in DeFi

The major risk in DeFi comes from poorly written smart contracts. A poorly written smart contract can be susceptible to hacks where the money market can lose all its funds. The other risks with DeFi are the volatility of cryptocurrencies and the quickly changing APYs.

# Conclusion

DeFi is hugely disrupting traditional finance. The permissionless, open and censorship-resistant nature of DeFi is crucial in the present condition where government policies and traditional banks are not built to serve the common people. I’m incredibly bullish on DeFi. Always DYOR.

# About Me

I have been developing on Ethereum for a while now and have been investing in various cryptocurrencies as well. I mostly work with smart contracts and solidity programming. If you have a fun project in mind and want to collaborate with a solidity developer, hit me up :)

You can find me on [Twitter](https://twitter.com/CleanPegasus) 我在这里发布关于区块链发展和加密的一般信息。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 另外，阅读

*   [最佳加密交易信号电报](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b) | [MoonXBT 评论](/coinmonks/moonxbt-review-6e4ab26d037)
*   [Coinswitch 俱吠罗评论](/coinmonks/coinswitch-kuber-review-1a8dc5c7a739) | [电网交易机器人](https://blog.coincodecap.com/grid-trading) | [比特币基地收费](/coinmonks/coinbase-fees-831e77d4f2c5)
*   [Bitget 回顾](https://blog.coincodecap.com/bitget-review)|[Gemini vs block fi](https://blog.coincodecap.com/gemini-vs-blockfi)|[OKEx 期货交易](https://blog.coincodecap.com/okex-futures-trading)
*   [OKEx vs KuCoin](https://blog.coincodecap.com/okex-kucoin) | [摄氏替代品](https://blog.coincodecap.com/celsius-alternatives) | [如何购买 VeChain](https://blog.coincodecap.com/buy-vechain)
*   [币安期货交易](https://blog.coincodecap.com/binance-futures-trading)|[3 comas vs Mudrex vs eToro](https://blog.coincodecap.com/mudrex-3commas-etoro)
*   [在印度利用加密套利赚取被动收入](https://blog.coincodecap.com/crypto-arbitrage-in-india)
*   [德国最佳加密交易所](https://blog.coincodecap.com/crypto-exchanges-in-germany) | [WazirX P2P](https://blog.coincodecap.com/wazirx-p2p)
*   [如何购买 Monero](https://blog.coincodecap.com/buy-monero) | [IDEX 评论](https://blog.coincodecap.com/idex-review) | [BitKan 交易机器人](https://blog.coincodecap.com/bitkan-trading-bot)
*   如何在 Bitbns 上购买柴犬(SHIB)币？ | [印度的币安](https://blog.coincodecap.com/binance-in-india)
*   [币安 vs 比特邮票](https://blog.coincodecap.com/binance-vs-bitstamp) | [比特熊猫 vs 比特币基地 vs Coinsbit](https://blog.coincodecap.com/bitpanda-coinbase-coinsbit)
*   [如何购买 Ripple (XRP)](https://blog.coincodecap.com/buy-ripple-india) | [非洲最好的加密交易所](https://blog.coincodecap.com/crypto-exchange-africa)
*   [非洲最佳密码交易所](https://blog.coincodecap.com/crypto-exchange-africa) | [胡交易所评论](https://blog.coincodecap.com/hoo-exchange-review)
*   [eToro vs robin hood](https://blog.coincodecap.com/etoro-robinhood)|[MoonXBT vs Bybit vs Bityard](https://blog.coincodecap.com/bybit-bityard-moonxbt)
*   [Stormgain 回顾](https://blog.coincodecap.com/stormgain-review) | [Bexplus 回顾](https://blog.coincodecap.com/bexplus-review) | [币安 vs Bittrex](https://blog.coincodecap.com/binance-vs-bittrex)
*   [Bookmap 评论](https://blog.coincodecap.com/bookmap-review-2021-best-trading-software) | [美国 5 大最佳加密交易所](https://blog.coincodecap.com/crypto-exchange-usa)
*   [如何在 FTX 交易所交易期货](https://blog.coincodecap.com/ftx-futures-trading) | [OKEx vs 币安](https://blog.coincodecap.com/okex-vs-binance)
*   [如何在势不可挡的域名上购买域名？](https://blog.coincodecap.com/buy-domain-on-unstoppable-domains)