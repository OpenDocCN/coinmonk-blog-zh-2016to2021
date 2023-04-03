# 分析区块链中的效用令牌值捕获

> 原文：<https://medium.com/coinmonks/analyzing-utility-token-value-capture-in-blockchain-bb0bd4fedb4c?source=collection_archive---------2----------------------->

![](img/5a27bc0a712bee84d42cbdf3fb92f71b.png)

代币经济学是对数字代币激增所带来的新经济模型的快速扩展研究。区块链技术的大量使用案例只是增加了这些新的基于令牌的经济模式的复杂性和重要性。

虽然在投资任何代币之前，项目质量、团队能力和市场生存能力仍然是需要考虑的关键问题，但在确定区块链领域的强大、高收益代币投资时，理解数字资产背后的代币经济学是一个重要且经常被忽视的因素。分析令牌模型如何与项目的总体商业模型相互作用，可以证实技术价值，并揭示有关网络和投资潜力的关键细节。在确定一个高质量的项目后，应说明代币在区块链生态系统中的用途，以确定其获取该项目所创造价值的能力。

在这个分析中，我将讨论:

*   公用事业代币如何在区块链增值
*   实用令牌特征及其与网络价值获取的关系
*   影响代币价值的代币经济模型机制
*   公用事业代币经济的当前问题
*   总结想法

出于此分析的目的，我将特别关注公用令牌，或授权访问项目中基于区块链的产品或服务的数字令牌，而不是安全令牌(数字证券化的真实世界资产)，因为它们需要单独分析。

# **区块链实用令牌如何捕捉价值？**

**什么是代币价值？** 在深入研究代币如何获取价值之前，有必要先定义什么是代币价值。由于公用事业代币的本质是代表在特定区块链内对服务的访问，代币的价值直接对应于基础区块链的价值。

然而，在这一基础价值和代币的实际交易价值之间有一个重要的区别。在区块链空间中，需要买家和卖家网络之间的协调来达到代币的交易价值，由于高投机和低使用率，代币的交易价值可能会大大偏离当前市场中的基础价值。随着市场的成熟，交易价值和潜在价值应该会趋同。

在这个分析中，我将关注一个评估潜在代币价值的框架。

**代币价值与网络价值** 虽然经常进行类比，但代币不同于股票，因为代币不代表公司或实体的所有权。这意味着基于股权的估值模型不能盲目应用于象征性估值。如上所述，公用设施令牌代表项目的区块链网络中的公用设施。因此，这些令牌的价值基于他们项目的网络效应，但*也基于*对区块链服务的需求和对令牌的需求之间的相关程度。

**确定网络价值** 大多数领先的区块链网络估值都是基于梅特卡夫定律。该定律表明，对于 n 个用户，网络的价值大约增加 n2。简单地说，每增加一个用户，网络效应就会成倍增加。考虑到特定代币和区块链市场的细微差别，该定律的变体也正在接受测试，例如由 Andrew Odlyzko 等人提出的一种方法，该方法采用了更保守的假设，并提出对于较大的 n 值，网络的价值增长更接近与 n*log(n)成比例。

**确定网络价值和代币价值之间的相关性** 难题的另一部分涉及分析代币的特征和经济模型，以确定其与基础网络价值的相关性。代币的潜在价值，以及最终区块链项目的成功，在很大程度上取决于代币的经济和激励结构。对代币特性和经济设计的研究是理解代币需求和网络价值之间关系的关键。

# **实用令牌功能**

**产品访问/贡献** 或许公用事业令牌最明显的特征是它能够访问或贡献其区块链本土的产品或服务。当本地代币是区块链服务的唯一可接受的支付形式时，该特征与价值最直接相关，因此允许代币获取更多网络价值。

**工作奖励(采矿/打桩)** 代币模式为在区块链网络内完成的工作提供奖励，特别是对交易进行验证，已经成为该领域最普遍的做法。代币的共识模型可以决定在原生区块链上工作的报酬性质。当今业界最常用的两种共识模型是工作证明和利益证明。

在工作证明(PoW)共识模型中，矿工通过求解复杂的数学方程来验证交易和创建新区块，从而获得奖励。众所周知的例子有[比特币](https://bitcoin.org/)和 [Zcash](https://z.cash/) 。PoW 提供了抵御攻击的安全性，但是其高能耗率会使挖掘成本对于普通用户来说过高，并且挖掘专业化会导致节点集中化。

在利益证明(PoS)模式中，代币持有人被激励持有代币以验证交易，较大的赌注者(称为“伪造者”)按比例获得奖励。赌注凭证的例子包括[破折号](https://www.dash.org/)和 [NEO](https://neo.org/) 。虽然，PoS 模型面临的主要问题是“无利害关系”问题，在分叉的情况下，伪造者被激励去验证两个链，阻止了共识并增加了双重支出问题。

在这两种共识模型中，对协议的更高需求导致更大的网络价值，这激励用户(无论是矿工还是伪造者)在其区块链上执行工作，从而增加令牌需求。混合模型也被引入，以解决这些流行的共识模型所面临的问题，同时保持代币激励结构和网络价值的相互联系。

**利润分享和使用激励** 一些代币被设计成在用户之间重新分配网络的现金流。KuCoin 是这方面的一个主要例子，因为他们的本地 KuCoinShares token (KCS)使持有者有权获得交易所交易费用的 50%。然后根据用户持有的股份按比例分配利润份额。KuCoin 还根据用户持有的 KCS 股票提供高达 30%的交易费折扣。这些激励措施不仅增加了对 KCS 的需求，也让令牌获得了更大的网络价值份额。

相比之下，[币安](https://www.binance.com/)，最大的加密货币交易所之一，在其本土的币安硬币(BNB)模式中没有利润分享机制。为了鼓励 BNB 的使用，交易费在 BNB 支付时会打 50%的折扣。然而，由于 BNB 没有额外的奖励来激励用户持有代币，而且币安交易所允许用比特币和以太坊等流行的代币支付交易费用，代币未能占据相当大的一部分网络价值。这凸显了强有力的代币激励结构在网络价值获取中的重要性，以及当允许在单一平台上以多种货币支付时的潜在陷阱。

**治理** 虽然看起来在区块链上享有治理权或投票权的代币对代币来说是一种显著的增值，但潜在的代币购买者必须谨慎权衡这一与代币价值相关的特性。

由 [Saman Adhami 等人](https://re.public.polimi.it/retrieve/handle/11311/1056221/292387/JEP%20published%20ICOs.pdf)进行的一项研究分析了 2014 年至 2017 年间 253 家 ico 的数据，发现 ico 在二级市场上的成功相对来说没有受到向令牌持有者授予网络治理权的影响，这意味着网络需求和令牌需求之间的相关性较低。虽然，他们的发现确实揭示了二级市场的成功与提供产品或服务的令牌和利润分享结构之间的密切关系，证实了前面几节中的主张。

除了高估基于令牌的治理的价值之外，潜在的令牌持有者还必须小心令牌可能被归类为证券。分散自治组织(Dao)是基于令牌治理的区块链生态系统的典范。然而，2017 年 7 月，[SEC 发布了一份报告](https://www.sec.gov/news/press-release/2017-131)，称所有 DAO 代币都将被视为证券，这对代币持有者和实体本身都造成了困难的法律局面。我将在后面的分析中进一步讨论关于实用程序令牌管理的问题。

# **代币经济模式**

**供应** 在考虑潜在代币价值获取以及项目的整体成功时，代币的供应和分发细节非常重要。

代币供应可以封顶或允许发行额外的硬币。例如，比特币的上限为 21，000，000 个硬币，由于大约的流通供应数据和开采率数据可以公开获得，因此与通过额外的硬币生成而面临稀释风险的代币相比，估值可以更加确定。

代币可以追求的另一个模型是烧钱和造币的平衡。在这种模式下，本地令牌必须是网络上唯一接受支付的令牌。当向服务提供商付费时，用户烧掉令牌，而不是将实际的令牌发送给提供商，当网络平衡运行时，网络铸造并奖励服务提供商适当的令牌量。Multicoin Capital 的凯尔·萨马尼(Kyle Samani)指出[这种代币设计创造了一种模型，在这种模型中，网络增长导致代币价值的线性、非投机性增长](https://multicoin.capital/2018/02/13/new-models-utility-tokens/)，从长期来看，这种代币是代币的一种更强有力的替代选择，因为代币的经济模式涉及明显的通缩燃烧，而没有铸造新的代币。

**单令牌经济与多令牌经济** 令牌经济结构的另一个区别在于网络中不同令牌的数量。虽然单令牌模型是最常见的，并且通常使本地令牌能够捕获更多的网络价值，但是多令牌经济可以被设计为有效地捕获令牌内的网络价值。

近地天体是双令牌经济的一个例子，由近地天体和气体令牌组成。近地天体代币代表近地天体市场的股份，并为近地天体区块链提供投票权。天然气作为红利分配给 NEO 持有者，并可用于支付网络交易费用。在这个例子中，对 NEO 协议的需求与对 NEO 令牌的需求直接相关，并且令牌所有者被激励持有他们的 NEO 以从网络增长和红利中获取利润。

区块链多种货币经济面临的主要问题还是监管和证券分类的威胁。在大多数多令牌设计中，一个令牌的价值通常直接基于网络增长和未来收益，这使得它们成为 SEC 针对区块链空间中贴错标签的公用设施令牌进行监管的主要目标。

# **公用事业令牌经济的当前问题**

随着区块链技术在美国获得大量支持，政府机构正在审查和修改他们的政策，以规范区块链投资。区块链监管机构最热门的话题之一，是将代币销售归类为真正的公用事业代币销售事件还是伪装的证券发行。这一点很重要，尤其是在 ICO 和 raise 活动期间，因为它们会影响整个销售的合法性。精通金融的人可能熟悉豪威测试，并理解其对区块链代币的潜在应用。

对于那些不熟悉的人来说，这是一条适用于区块链的一般经验法则:在立法者眼中，出售的代币如果没有最低限度的可行产品来行使代币的效用，可能会被视为证券。在这种情况下，代币在没有推出产品或服务的情况下出售，也可以预期，即使项目的需求与代币本身的需求相关，网络的基础价值和代币的交易价值也会由于代币效用的缺乏而发生很大变化。

**网络使用与投资/投机** 当今代币经济面临的另一个主要问题是实际网络使用与投资投机之间的巨大差距。当前区块链市场上的大多数代币持有者并不将代币用于其预期用途，无论是作为支付和财富储存单位，还是用于区块链的产品或服务，而是用于货币收益。这增加了用户数据，可能会破坏网络活动的增长，减缓这些经济的成熟，并操纵代币的交易价值。

速度问题——汇率的宏观经济概念是由交易量和货币速度共同驱动的——也扭曲了交易货币的价值。虽然在区块链空间中准确预测速度非常困难，丢失代币或钱包仍然是影响真实流通指标的问题，但代币经济学仍然可以帮助投资者确定代币，从而从他们的网络中获取最大价值。

# **总结思路**

在目前的区块链市场中，中长期价格预测是徒劳的，但使用理论框架来分析代币结构的价值可以帮助识别强劲、高收益的区块链投资。在评估一个项目时，技术质量和市场生存能力仍然是要考虑的首要和最重要的因素。也就是说，对功能和经济设计的研究可以确定一个强大项目的令牌如何有效地捕捉其网络的价值。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)