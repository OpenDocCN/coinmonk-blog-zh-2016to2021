# 闪电网络中的流动性提取

> 原文：<https://medium.com/coinmonks/liquidity-abstraction-in-lightning-network-3d7a1d76ac82?source=collection_archive---------2----------------------->

![](img/6ff84f919af6557fe7ea65b630d56899.png)

# 介绍

虚拟支付渠道的想法最初是由闪电网络(LN)的开发者 Rene Pickhardt 在他的博客中提出的。这些特定渠道的主要优势可能在于提高大型支付提供商的可扩展性和可靠性。除了纯粹的技术优势之外，这种类型的渠道可能被具有某些信任假设的交易对手用于增加其路由的可靠性，同时降低链上成本。作者将虚拟支付通道的创建视为等同于无中生有的 satoshis 的创建，并因此假设他们通常在 LN 中启用部分准备金。

Rene Pickhardt 的帖子启发了比特币闪电钱包开发者 Anton Kumaigorodsky 创建所谓的[托管频道](https://github.com/btcontract/hosted-channels-rfc)。BLW 的用户可以在几秒钟内引导这些信道。与其他设计决策一起，它允许 BLW 用户比市场上任何其他可用的钱包更快地准备接收 LN 支付。然而，即使在几年的成功运营之后，托管渠道的概念在很大程度上仍然不为更广泛的闪电网络和比特币社区所知。

在本帖中，我们考虑托管频道的主要属性，并评估它们的经济效果。与此同时，第一个移动优化的 LN 图书馆 [IMMORTAN](https://github.com/btcontract/IMMORTAN) 也将采用公共托管频道。

# 部分储备或比特币定义

部分准备金是对银行系统的一般定义，银行系统的存款与信贷比率不是 1 比 1。

![](img/ab92a60b7541df03bc7209160a9ad126.png)

[http://www.bondeconomics.com/2016/11/primer-monetary-aggregates.html](http://www.bondeconomics.com/2016/11/primer-monetary-aggregates.html)

在现代世界，纸币和部分准备金一起创造了巨大的信用扩张。需要强调的是，部分准备金构成了当代金融体系的基础。对于每一个账户单位，商业银行被允许发放大约 10 倍以上的单位，以通过信贷为他人的运营提供资金，而我关于实际比例的信息可能已经过时。根据债务人的外部评级或等级，偿还债务的义务几乎在任何地方都可以被用作资产。这些存款只是冰山一角。

相反，托管渠道允许利用完全建立在硬钱基础上的支付网络中的信任。当建立时，这种渠道在发起渠道创建的接收方具有零流动性。接收者生成新的发票，然后 satoshis 被路由到他的托管频道。在网络层面，什么都没有改变，没有额外的 satoshis 创建或破坏。然而，托管频道提供商现在有责任根据需要将付费从托管频道路由到任何网络节点。这一刻定义了主人和客户建立起相当于借钱的信任关系的实际情况。

DeFi 项目通常将贷款宣传为使用以太坊协议实现的里程碑。但这只是营销。顾名思义，信用需要信任，在去中心化的世界里，通常是平台无法抵御 Sybill 的攻击。托管渠道提供商可以至少使用其恒定的域名来建立声誉，并因此在数字世界中复制已知的概念，如公司或商标。再往前我们可能会记得，在闪电网络托管的频道提供商已经将其公钥与网络八卦中的相关历史记录联系起来，甚至部分记录在比特币区块链中。闪电节点具有建立良好声誉的天然动机，这可以在托管信道中创建经济上可扩展的信任关系中加以利用，同时与网络的其余部分按照共同标准进行操作。这类渠道是真实而廉价的“支票账户”。

> 我们在谈论信用分析。所以，请:让我们停止称之为法定货币。让我们开始称之为:信用货币。[来源](https://www.ft.com/content/5e5b2afb-c689-4faf-9b47-92c74fc07e66?segmentid=acee4131-99c2-09d3-a635-873e61754ec6)

在某种程度上，在创建托管渠道后，客户现在在提供商的渠道中租用流动性。然而，提供商仍然可以使用它来进行路由，并且它不会陷入 LN 图的死胡同。在较低的层面上，该系统现在看起来很像中世纪早期的银行，采用[复式记账](https://en.wikipedia.org/wiki/Double-entry_bookkeeping)，从而降低了黄金和白银的交易成本。在更高的层面上，所有的东西都接入了基于开放标准的闪电网络，提供商们相互竞争，提供更好的全天候服务。主机和客户端之间的信任关系很像自由银行时代的银行客户。正如 Lawrence H. White 在他的书《英国的自由银行业:理论、经验和争论，1800-1845，第二版》中所写的，苏格兰的自由银行体系是强健而有弹性的。

> “……苏格兰自由银行业最显著的特征之一:银行股东的无限责任。尽管数额巨大，艾尔银行的损失完全由其 241 名股东承担。包括票据持有人在内的债权人的债权得到了全额偿付。”(第 29 页)

我们没有足够的历史数据来判断 LN 运营商，但我们可以直接将苏格兰银行的股权资本要求转化为比特币闪电网络的实际资本需求。

# 评估经济效果

[“闪电网络经济学:渠道”](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3840374)(或参考下面的视频)的作者对闪电节点经济学进行了理论研究，将 [**排队论**](https://en.wikipedia.org/wiki/Queueing_theory) 应用于支付系统，并比较了单向(也称为不平衡)和双向(平衡)两种配置的渠道成本。

假设总共有 19990 个托管频道，我们一年大约有 76580 个交易。这些数字是在与 BLW 开发商 Anton Kumaigorodsky 的个人交流中获得的。它带来的平均支付率等于 0.26。在前面提到的论文中，作者考虑的最大支付率是 0.2，反映机会成本的最大利率等于每年 1%。当支付率接近最大值 0.2 时，如果对方不进行任何支付(因此其支付率为 0)，则单边渠道是可靠的。

根据 Guasoni 等人的论文，我试图研究可能的利率，这些利率可能使通过托管渠道支付成为不可靠的行为，并模拟了正常渠道的最优单边渠道成本。在作者选择的 r=1%时，有可能在 0

![](img/8119caaae181c6edb90a87ffbe41b217.png)

最初的模型是为“真正的”不可信 LN 通道构建的，但是托管通道不会产生任何区块链足迹，因此没有相关的在线成本。这意味着我们可以抵消值 B=1 的经济效应水平线(虚线),并将这种差异视为托管频道相对于常规闪电频道的竞争优势。它为我们提供了一个λ= 0.2 的点，在这个点上，用户使用在线交易或使用托管渠道的流动性进行支付是同样有价值的。事后看来，让 BLW 实施这些技术比其他钱包提供商更有竞争优势，而且基础设施成本也可能是最低的。

# 结论

从技术角度来看，托管渠道提供商有一种有效的方法来降低维护网络其余部分的托管支付服务的成本。终端设备是唯一的，并且具有由 PIN 或生物统计学数据保护的其自己的密钥，因此反欺诈保护对于该服务来说不成问题。这种网络中 Sybils 相对于普通用户也没有优势，因为基本配置中的提供商不提供流动性，且用户在开始使用它们之前必须将 satoshis 存入托管信道。

托管渠道的进一步发展可能包括风险更大、保证更少的技术，如创建具有初始存款的托管渠道，这相当于在部分准备金银行中创建信用货币，或创建具有不同于 LN 基本货币面额的托管渠道。在这一点上，托管频道提供商实际上不再是一个完全的储备银行，并将其声誉置于危险之中，而且必须管理各种风险。历史上有一些相对稳健的银行系统在准备金率低于 5%的情况下运行的例子。目前还不知道一些储备证明技术是否可以应用于这些银行。

如果你喜欢这篇文章，请不要犹豫通过 [LNURL 捐赠。](https://lntxbot.com/@notgeld)

![](img/4d471065e0994abeb27cd394c0550142.png)

> 加入 [Coinmonks 电报频道](https://t.me/coincodecap)，了解加密交易和投资

## 另外，阅读

[](/coinmonks/why-bitcoins-lightning-network-is-the-ingenious-10dc1ad9ccae) [## 为什么比特币的闪电网络很巧妙

### 有三种会计制度:单式、复式和三式。区块链和比特币的…

medium.com](/coinmonks/why-bitcoins-lightning-network-is-the-ingenious-10dc1ad9ccae) [](/coinmonks/the-lightning-network-how-to-install-and-hopefully-make-money-6e3058e3fa7c) [## 闪电网络:如何安装和(希望)赚钱

### 注:本文档写于 2018 年 7 月。闪电是一种新技术，进步很快。如果您发现任何过时的…

medium.com](/coinmonks/the-lightning-network-how-to-install-and-hopefully-make-money-6e3058e3fa7c) 

*   [印度加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9) | [比特币储蓄账户](/coinmonks/bitcoin-savings-account-e65b13f92451)
*   [币安期货交易](https://blog.coincodecap.com/binance-futures-trading)|[3 commas vs Mudrex vs eToro](https://blog.coincodecap.com/mudrex-3commas-etoro)
*   [如何购买 Monero](https://blog.coincodecap.com/buy-monero) | [IDEX 评论](https://blog.coincodecap.com/idex-review) | [BitKan 交易机器人](https://blog.coincodecap.com/bitkan-trading-bot)
*   [CoinDCX 评论](/coinmonks/coindcx-review-8444db3621a2) | [加密保证金交易交易所](https://blog.coincodecap.com/crypto-margin-trading-exchanges)
*   [CoinLoan 评论](https://blog.coincodecap.com/coinloan-review) | [YouHodler 评论](/coinmonks/youhodler-4-easy-ways-to-make-money-98969b9689f2) | [BlockFi 评论](https://blog.coincodecap.com/blockfi-review)
*   [CoinFLEX 评论](https://blog.coincodecap.com/coinflex-review) | [AEX 交易所评论](https://blog.coincodecap.com/aex-exchange-review) | [UPbit 评论](https://blog.coincodecap.com/upbit-review)
*   [AscendEx 保证金交易](https://blog.coincodecap.com/ascendex-margin-trading) | [Bitfinex 赌注](https://blog.coincodecap.com/bitfinex-staking) | [bitFlyer 点评](https://blog.coincodecap.com/bitflyer-review)