# 以太坊扩展解决方案指南

> 原文：<https://medium.com/coinmonks/guide-to-ethereum-scaling-solutions-7ef55423f47e?source=collection_archive---------5----------------------->

![](img/6f99faf45b781b5760fc67dcc4975f6c.png)

**这篇文章记录了我理解以太坊扩展解决方案的旅程，没有任何预先的编码或更深入的技术知识。**

沿着这条路走下去，阅读上面提到的文章或者至少是我的简要总结。我花了大约六个小时来阅读，尤其是理解所有的文章。所以花些时间，让我们开始吧！

1.  **首先，我们将阅读一个简短的** [**twitter 帖子**](https://twitter.com/alex_kroeger/status/1463329118339141633?t=JJ3IptTbXhKd4xfbbhsoUg&s=19) **作者** [@alex_kroeger](https://mobile.twitter.com/alex_kroeger) ，**向我们介绍为什么我们需要扩展解决方案、两种主要方法以及它们如何使以太坊网络受益。**

这条线索应该是不言自明的，不需要摘要。

**2。在深入探讨具体解决方案(主要是汇总)之前，我们继续简单概述当前的以太坊扩展解决方案。**

[](https://defipulse.com/blog/a-beginners-guide-to-ethereum-scaling-solutions/) [## 以太坊扩展解决方案初学者指南

### 以太坊是一个铁板一块的区块链:它提供自己的安全性，执行自己的交易，维护自己的…

defipulse.com](https://defipulse.com/blog/a-beginners-guide-to-ethereum-scaling-solutions/) 

扩展整体以太坊区块链的解决方案概述(离线和在线):

*   **Zk-rollups** :通过 zk-SNARKs 的链外执行+链上数据
*   **乐观汇总**:链外执行+链内数据+欺诈证据
*   **验证**:链外执行+零知识证明+链外数据
*   **意志**:链外执行+链内或链外数据可用性(可选)
*   兼容以太坊的区块链，不要直接继承以太坊的安全性
*   **状态通道**:支付通道，关闭后在以太坊上完成
*   **Plasma** :依赖防欺诈，但维护离线数据可用性
*   **分片**:将以太坊协议分散到 64 个分片上，效果乘以上滚

**3。让 Vitalik 向我们解释什么是三种主要的 L2 标度，但我们在等离子体之后停止，因为我们需要一个更容易的开始进入卷的主题。**

[](https://vitalik.ca/general/2021/01/05/rollup.html) [## 不完整的汇总指南

### 首先，Bob 将 1 美元(或一些 ETH 或 stablecoin 等价物)放入智能合约。向爱丽丝支付他的第一笔钱…

vitalik.ca](https://vitalik.ca/general/2021/01/05/rollup.html) 

L2 缩放有三种主要类型:

**状态通道:**

*   用户可以打开一个支付通道来相互交易，而无需在链上发布交易。一旦完成交易，他们可以关闭渠道，只有最终的收支差额会被公布
*   要求锁定一定数量的资本
*   资金只能发送给渠道中的参与者

**血浆:**

*   提供比通道更强的属性
*   资金可以发送给不是参与者的用户
*   资本要求较低
*   数据不需要在链上传输
*   转移不是即时的，只有在间隔(如 7 天)后
*   依赖第三方“运营商”
*   是特定于应用程序的，不能模拟完整的以太坊环境

为了解决这个问题，我们开始-> **汇总**

**4。首先，我们将阅读 Mechanism Capital 的“非技术性总结介绍”,并遵循 Benjamin 的比喻，因为它将帮助我们理解基础知识。**

[](https://www.mechanism.capital/rollups-introduction/) [## 汇总的非技术性介绍

### 作者:本杰明·西蒙 2021 年 6 月 1 日让我们从一个比喻开始。想象一下，我们又回到了中学数学课…

www.mechanism.capital](https://www.mechanism.capital/rollups-introduction/) 

本文通过应用一个隐喻来理解汇总的必要性，以一种简单易懂的方式解释了汇总。

*   汇总是具有特定修改的独立区块链，但通过智能合约与以太坊关联。
*   汇总执行事务并处理数据。以太坊存储结果。
*   集中汇总可以有效地处理事务。提交的批量交易在以太坊上进行审查，只有在以太坊一致同意的情况下才能最终确定。因此，再次去中心化(与侧链相反)。

**5。我们继续沿着这条路走下去,(几乎)我们需要知道的关于乐观汇总的一切。**

[](https://www.paradigm.xyz/2021/01/almost-everything-you-need-to-know-about-optimistic-rollup/) [## (几乎)你需要知道的关于乐观汇总的一切

### 以太坊生态系统中最大的挑战之一是在资源紧张的情况下实现低延迟和高吞吐量…

www.paradigm.xyz](https://www.paradigm.xyz/2021/01/almost-everything-you-need-to-know-about-optimistic-rollup/) 

介绍重要的区块链术语和可伸缩性问题(增加信任或成本)。如何在不“有意义地”改变信任和成本假设的情况下扩展以太坊。

*   Sequencer 计算链外的所有东西，只将 merkle 根发布到以太坊。
*   以太坊上第 2 层相关数据的数据可用性“防止”不可用或恶意序列发生器的损害。
*   在争议期间，各方(验证者)可以提交欺诈证明，以表明定序器发布了无效的状态转换。
*   测序人员必须在以太坊上贴一张“忠诚保证书”，如果欺诈被证实，该保证书将被没收(以抑制恶意行为)。如果核查人员发现欺诈行为，他们将获得债券的分配。
*   通过提前运行，一个恶意的定序器可以在其他人面前争论自己，并接受自己的债券惩罚->部分债券应该被烧毁。
*   批评:长时间的退出/争议期对采用是致命的->做市商提供的费用的快速退出。

**6。现在我们可以继续阅读 Vitalik 的文章。他将向我们介绍 zk-rollups 以及给我们一个更深入的见解。我总是很难理解 Vitalik 的文章，这就是为什么我们必须先阅读其他文章。**

[](https://vitalik.ca/general/2021/01/05/rollup.html) [## 不完整的汇总指南

### 首先，Bob 将 1 美元(或一些 ETH 或 stablecoin 等价物)放入智能合约。向爱丽丝支付他的第一笔钱…

vitalik.ca](https://vitalik.ca/general/2021/01/05/rollup.html) 

(我将跳过这里已经提到的要点)

*   汇总是完全通用的，甚至可以在汇总中运行 EVM，允许现有的以太坊应用程序迁移到汇总，几乎不需要编写任何新代码。
*   智能合同链维护最新的状态根(汇总状态的 Merkle 根(帐户余额、合同代码…))。
*   通过发布一个批处理(事务以及以前的状态根和新的状态根的集合)，契约检查批处理中以前的状态根是否与其当前的状态根匹配，如果匹配，则将状态根切换到新的状态根。

**我们怎么知道新的州根是正确的？**

*   乐观累计->防欺诈
*   ZK-汇总->有效性证明

对于这两种类型之间的权衡，看一下表->短期乐观汇总可能会赢，中期和长期 ZK-汇总。

**欺诈证明:**

*   如果无效批次已经公布，任何人都可以提交欺诈证明，证明该批次是无效的。
*   欺诈证明将包含批处理本身和 Merkle 树的一部分，只需要证明被修改的特定帐户。

**压缩:**

*   由于出色的编码和压缩技巧，汇总时的 ETH 传输占用的字节明显更少。

**谁可以提交批次？:**

*   为了做到这一点，用户必须提交一大笔押金，在欺诈批次的情况下，部分押金将被烧毁，部分给欺诈证明者(更多可能性，见文章)。

**汇总能给你多大的伸缩性？:**

*   45 TPS 到 4807 TPS(仅 ETH 传输)
*   范围从 105 倍(以太网转账)到 570 倍(保护隐私的提款)
*   分片将进一步成倍增加缩放比例

**尚未完全解决的挑战:**

*   用户和生态系统入门
*   交叉累计交易
*   对缺失序列器的响应…

**7。你现在头疼吗？如果没有，让我们看看目前市场上乐观的汇总解决方案。**

[](https://mirror.xyz/dcbuilder.eth/QX_ELJBQBm1Iq45ktPsz8pWLZN1C52DmEtH09boZuo0) [## 以太坊 L2s 终极指南—镜子

### 我是 DCBuilder，Moralis 的区块链研究员，在这里我写了关于 DeFi、NFT、DAOs、L2s、MEV 和其他各种…

镜像. xyz](https://mirror.xyz/dcbuilder.eth/QX_ELJBQBm1Iq45ktPsz8pWLZN1C52DmEtH09boZuo0) 

一直读到零知识总结部分。

L2 是一种具有独立执行层的扩展解决方案，继承了运行于其上的网络的安全性和分散性。

**L2 缩放解决方案可以根据证明类型进行分类:**

*   有效性=利用零知识的数学证明
*   欺诈证据=引入争议时间延迟(争议期)

**和数据可用性:**

*   在线:状态和执行数据可以在线访问和验证
*   离线:数据由 L2 离线处理和保存(更高的可伸缩性，但不太安全和分散)

**当我们可以攀登 L1 时，为什么选择 L2s？:**

*   单片区块链:Solana 通过升级到性能更好的硬件来增加计算能力(见 [@gakonst](https://www.paradigm.xyz/2021/01/almost-everything-you-need-to-know-about-optimistic-rollup/) 可扩展性问题)
*   模块化区块链:主网络充当数据可用性层(优先考虑安全性和分散性)，L2 充当顶层的执行层。在技术和经济上都是可持续的

**乐观累计**

**仲裁者:**

*   7 天争议时间延迟
*   欺诈证据和链上数据可用性

**乐观:**

*   仍然门控
*   在欺诈证明、验证和 EVM 等效与兼容方面与 Arbitrum 不同

**博巴网**

*   最初乐观的分叉
*   通过社区驱动的流动性池快速退出(仅几分钟)
*   降低天然气费用、提高交易吞吐量和扩展智能合同功能

**梅蒂斯**

*   分片乐观汇总架构->每个 DAC(分散自治公司)都可以定制，因此是根据计划在完全互操作的同时扩展其运营的集团而专门构建的

**8。其次是当前的 zk-rollup 解决方案。**

[](https://mirror.xyz/dcbuilder.eth/QX_ELJBQBm1Iq45ktPsz8pWLZN1C52DmEtH09boZuo0) [## 以太坊 L2s 终极指南—镜子

### 我是 DCBuilder，Moralis 的区块链研究员，在这里我写了关于 DeFi、NFT、DAOs、L2s、MEV 和其他各种…

镜像. xyz](https://mirror.xyz/dcbuilder.eth/QX_ELJBQBm1Iq45ktPsz8pWLZN1C52DmEtH09boZuo0) 

现在继续 zk-rollups 部分。

**零知识汇总:**

*   给出陈述和条件的数学证明，但不透露这样做所需的任何信息
*   使用有效性证明
*   每个交易批次都包含一个发布到以太坊主网的有效性证明
*   比乐观汇总更早的开发阶段

zkSync:

*   ZK-汇总 L2 网络
*   尚不兼容 EVM，支持支付功能，有限的智能合同，NFT 铸币…
*   testnet 上有 EVM 兼容版本

Starkware:

*   开发基于 STARK 的解决方案的公司
*   使用完全不可信设置的有效性证明形式

StarkEx:

*   Starkware 开发的 Zk-L2
*   目前支持许多功能(意志，快速提款，NFT 铸造，自我保管…)

**dYdX:**

*   在 Starkware zk-rollup(自定义 StarkEx 实施)上运行的衍生平台
*   订单簿在 L2，允许便宜的交易

**StarkNet:**

*   无权限、分散的 zk-rollup
*   将支持 EVM 兼容性
*   2021 年 11 月获得阿尔法许可

**多边形赫米兹:**

*   无权限、分散的 zk-rollup
*   尚不兼容 EVM
*   被收购并融入多边形生态系统

**阿兹特克网络:**

*   注重隐私的 zk-rollup
*   以私人方式进行快速廉价的 DeFi 兼容交易

**Loopring:**

*   开源 zk-rollup 协议
*   智能合约和 zk 电路的集合，用于构建高度安全且可扩展的 DEXes、AMM 和支付应用

**快速离题到有效/意志:**

*   混合扩展方法->不将呼叫数据放在链上
*   有效性:有效性证明，但外链数据
*   意志:用户可以选择每笔交易使用 validium 还是 zk-rollup。用户选择最大化区块链三元悖论的哪些方面

**Alpha 部分:**现在使用 L2s 和协议，以便为以后可能的空投做准备

**当前缺点:**

*   分散的流动性
*   还没有跨 L2 的 AMMs
*   尚未完成 EVM 兼容性或等效性
*   部分基础设施目前是集中式的
*   没有直接法令(币安现在允许直接撤回到亚美尼亚)

有关 L2 相关项目和工具的详细列表，请查看文章末尾。

如果你想更深入地研究某些 zk 项目或实际尝试它们，第 9 点和第 10 点适合你。

**9。要深入了解 Matter Labs (zkSync)和 Starkware (StarkEx 和 StarkNet)的 zk-rollup 项目，请查看 Bankless 的这篇文章。**

[](https://newsletter.banklesshq.com/p/the-best-comparison-on-zkrollups) [## 今天 zkRollups 上最好的对比

### 亲爱的无银行国家，不要相信 FUD。以太坊今天正在扩大规模。几个星期…

newsletter.banklesshq.com](https://newsletter.banklesshq.com/p/the-best-comparison-on-zkrollups) 

**10。试试 UniSync，它是 zkSync 2.0 上 Uniswap V2 的一个端口:**

[](https://blog.matter-labs.io/unisync-a-port-of-uniswap-v2-on-the-zkevm-b12954748504) [## 尤尼辛奇:位于兹克夫姆河畔尤尼斯瓦普 V2 的一个港口

### EVM 兼容的 zkRollup 上的第一个完整应用程序！

blog.matter-labs.io](https://blog.matter-labs.io/unisync-a-port-of-uniswap-v2-on-the-zkevm-b12954748504) 

11。现在我们知道了什么是乐观-和 ZK-上卷，让我们看看它们之间目前的区别。到目前为止，我们已经知道了他们中的大部分，但是这将帮助我们再次了解他们的不同之处和一些关于他们当前状态的信息。

[](https://insights.deribit.com/market-research/making-sense-of-rollups-part-one-optimistic-vs-zero-knowledge/) [## 理解汇总，第一部分:乐观与零知识

### “这是最好的时代，这是最坏的时代。”正如狄更斯对革命的欧洲的复述一样，它…

insights.deribit.com](https://insights.deribit.com/market-research/making-sense-of-rollups-part-one-optimistic-vs-zero-knowledge/) 

**两者在审核流程的工作方式上有所不同:**

*   Zk:发送批量交易+有效性证明到以太坊。以太网使用有效性证明来验证正确性
    - >“有罪直到被证明无罪”
*   乐观:争议期间的欺诈证据
    - >“在证明有罪之前是无辜的”
*   因此，zk ->无撤回延迟的优势(争议期)

**定义就绪:**

*   乐观:与 EVM 非常相似，项目迁移过程简单
*   Zk:伴随每种交易类型的有效性证明，使得汇总技术更难构建
*   乐观->解决当前问题/ zk ->还没有达到相同的可行性水平

**安检:**

*   Zk:如果事务执行第三方(“中继者”)离线，接管他的责任将更加困难，因为用 ZK-proof 构造块需要昂贵的计算基础设施
*   乐观:依赖于至少一个诚实的验证者

**用户体验:**

*   Zk:即时交易确认，选择加入和退出该层几乎没有延迟，而不是 1-2 周的取款延迟
*   乐观:做市商允许即时提款以换取费用(几天前不得不支付 30 美元从 BSC 桥到 Boba 桥(由于以太坊费用，仍然比使用标准 Boba 桥便宜))

**最终想法:**

*   从长远来看，扩展解决方案之间在成本、速度和用户体验方面的差异将趋于零。安全性和信任假设+网络效应将是主要区别。

现在应该可以了。我们了解了为什么我们需要扩展网络(以太坊)，存在什么类型的不同解决方案以及它们是如何工作的。

**下一步将是阅读在合并后的以太坊世界中，如何扩展汇总以及它们将如何受益于分片。**

[https://eth.wiki/sharding/Sharding-FAQs](https://eth.wiki/sharding/Sharding-FAQs)
https://notes.ethereum.org/@vbuterin/data_sharding_roadmap
https://hackmd.io/@canti/rkUT0BD8K

# 感谢阅读！

这篇文章代表了我对以太坊伸缩解决方案的了解。这是迄今为止不全面的，可能包括错误。
如果你认为我遗漏了重要的文章或方面，请联系我:**推特:**[**@ Pascal _ br**](https://twitter.com/pascal_br) **感言:**[**Pascal br . voice . site**](https://t.co/gidTut2Fvt?amp=1) **Pascal br . eth/wag mi🔥。sol**

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 也阅读

[](/coinmonks/leveraged-token-3f5257808b22) [## 杠杆代币[多头代币]终极指南

### 杠杆化令牌是具有杠杆化风险敞口的 ERC20 令牌，不考虑保证金、要求、管理…

medium.com](/coinmonks/leveraged-token-3f5257808b22) [](https://blog.coincodecap.com/crypto-exchange) [## 最佳加密交易所| 2021 年十大加密货币交易所

### 加密货币交易所的加密交易需要了解市场，这可以帮助你获得利润。之前…

blog.coincodecap.com](https://blog.coincodecap.com/crypto-exchange) [](https://blog.coincodecap.com/best-swap-platforms) [## 2021 年最佳加密交换平台| CoinCodeCap

### 如果我们看看今天的场景，许多加密货币交换平台提供了广泛的功能和深度…

blog.coincodecap.com](https://blog.coincodecap.com/best-swap-platforms)