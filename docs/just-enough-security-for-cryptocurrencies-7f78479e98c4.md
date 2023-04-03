# 加密货币的安全性刚刚好

> 原文：<https://medium.com/coinmonks/just-enough-security-for-cryptocurrencies-7f78479e98c4?source=collection_archive---------1----------------------->

伊泰·察巴里，亚历山大·斯皮格曼，伊泰·埃亚尔

如果你知道比特币等加密货币的两件事，其中之一可能是它们消耗能量。很大的力量。[据估计](https://www.cbeci.org/comparisons/)这些系统的能耗与澳大利亚和哥伦比亚等国家相当。

高功耗对许多加密货币的安全性至关重要:要破坏它们，攻击者需要消耗的功率相当于系统其余部分的总和。这一机制是有效的:主要加密货币表现出了前所未有的弹性，尽管不乏破坏其安全性和窃取金钱的动机。

但是安全门槛不夸张吗？为什么奥地利的电力消耗率是正确的？也许一半就够了？还是四分之一？可以说，过度支出的负面生态效应意味着我们应该以较低的支出率为目标，以实现*刚好足够的安全*。

![](img/b9ef563a8e87e9b31c65c69e5e6f55dc.png)

我们不知道多少功率是足够的，这本身就是一个话题。但是直到今天还没有办法调整它。根据现有协议，加密货币消费完全由市场力量决定，如下所示。加密货币通过铸造新硬币来增加供应量，作为对矿工的奖励，矿工是确保系统安全的参与者。奖励是对矿工资源支出的补偿，所有矿工花费的资源价值和铸造的硬币价值差不多。这一数量的资源和令牌是一个*安全阈值*，因为攻击者必须投入同样多的资源来操纵系统。

让我们设计一个新的系统，其中资源消耗是 1/4。这里有一个行不通的解决方案:铸币和奖励相比原系统只有 1/4 硬币。例如，如果原来的系统对每个动作奖励 10 个硬币，新系统对同样的动作会奖励 2.5 个。

原系统和新系统有相似的经济属性，比如货币流通速度，只有原系统比新系统多 4 倍的流通硬币。这意味着新系统中的每枚硬币的价值是原来硬币的 4 倍。因此，参与奖励和相应的支出实际上是相同的。

**我们提出了** **两个加密货币协议，允许它们的设计者以任意因子减少电力消耗**，以安全换取生态足迹。包括模型改进、协议和博弈论分析在内的全部细节都在我们的技术报告[中。](https://arxiv.org/abs/1911.04124)

通过本文的其余部分，我们将把我们的协议与经典的 Nakamoto 协议进行比较，Nakamoto 协议操作比特币和类似的系统，包括比特币现金、莱特币、ZCash 等。我们强调减少浪费的好处适用于任何 PoW 加密货币。

![](img/e91f8f63c7b960dc1cd0424a6710e6b9.png)

**部分奖励**

第一个协议被命名为*partialreadd*，通过减少矿工的利润来减少生态破坏。直觉很简单——减少采矿奖励，以降低资源消耗的盈利能力。它的工作原理如下。

*部分地区*铸币的汇率和中本聪的一样，因此两个系统中的每一枚硬币都有相等的价值。然而，在*partialredward*中，矿工只能得到一小部分铸造的硬币，而其余的硬币则根据他们相对的硬币持有量分配给所有的硬币持有者。因为采矿区块奖励的货币价值较低，所以与原始系统相比，矿工被激励花费较少的资源。

对于实施，分配矿工的奖励可以像任何中本聪系统。剩下的硬币可以通过更新每个客户的余额来分配。或者，为了避免量化问题，可以通过抽奖将金额授予单个客户地址。

虽然更加环境友好，但*partial ward*有一个明显的缺点——它降低了制造砖块所需的成本。与中本聪相比，如果矿工的奖励下降到 x%，攻击者体型的阈值下降 x%。因此，我们寻求另一种减少消耗而没有脆弱性困境的协议。输入*incentivenizedinternalexpenses*。

![](img/d28d2406f8aa38f90c4d1ebe8578ee4b.png)

**奖励性内部费用**

我们的第二个协议，*incentivenizedinternalexpenses*，被设计为在保持高攻击成本阈值的同时具有降低的资源消耗。我们将从解释该机制开始，然后回顾它的安全优势。

关键思想是通过激励(而不是强迫)来维持较高的块创建成本！)矿商将部分资金用于系统内部，而不是用于购买硬件和电力。与原始系统和部分系统一样，它也以相同的速度铸造硬币，保持每枚硬币的货币价值相等。

我们通过引入一个名为 *score* 的新属性来实现内部支出机制。矿工通过消耗资源来制造分数为 1 的区块，就像中本聪一样。或者，矿工可以创建分数为 *F* > *1* 的块，其中 *F* 是系统参数。要创建一个 *F* 分数区块，除了外部消耗的资源，矿工还必须在内部消耗加密货币硬币。这意味着，如果她想要创建 *F* -score 块，矿工就有更少的资金用于外部资源。(我们注意到共识机制与 Nakamoto 的机制相同，它不关心区块分数)

区块链在块的历元中前进，并且每个历元的奖励在历元结束时被分配给它的所有块。奖励总额和中本聪一样。然而，每个块根据其在该时段中所有分数中的分数比率而被奖励。

为了保持加密货币的价值不变，内部花费的硬币保持流通，并根据其相对硬币持有量重新分配给所有系统用户，如*部分向前*所示。

直觉上，设定一个足够高的系数块分数会激励矿商在内部支出，减少他们的外部支出。事实上，我们通过分析和数值评估证明了这一点。

为了讨论*incentivezidenteralexpenses*的安全优势，我们需要细化攻击成本的安全定义。假设攻击者想要重写历史，取消她过去对已经交付的兰博基尼的支付。为此，她必须创建一个替代的区块链分支，从而产生块创建成本。然而，一旦成功，她还会因为自己创造的格挡获得格挡奖励，使得攻击成本为零。因此，正确的衡量标准是执行此类攻击所需的前期成本。

在*incentivenizedinternalexpenses*中，攻击者有两条路径。第一种是在她的替代链上创建 *F* -score 区块，产生与其他矿工相同的成本，并要求相同的资源阈值。在这条道路上，一次成功的攻击是免费的，就像中本聪一样。或者，攻击者可以创建单独的规则块；然后，她可以将所有资金用于外部资源，以更快的速度和更低的成本建造砌块。然而，在这种途径中，当攻击成功时，她将不会得到对其支出的全部补偿。对于足够大的 *F* 来说，这不是一个有吸引力的选择(除非汽车价格大大超过被替换的块奖励，但所需的确认时间应该考虑到这一点)。

注意*incentivezidenteralexpenses*引入了另一个权衡。如果矿工被要求获得硬币以有效地参与，他们在某种意义上依赖于现有硬币持有者的许可来参与。如果他们没有这种许可，他们的开采工作将效率较低(他们只能生产 1 分的区块)，导致预期回报较低。换句话说，该系统不像中本聪的区块链那样没有权限，尽管它比股权证明系统更没有权限。

**结论**

总之，平衡系统安全和生态破坏是拓宽加密货币系统可用性的重要一步。我们介绍了两个区块链协议，实现减少生态足迹相比，现有的协议。他们第一次允许加密货币区块链的设计者调整他们区块链的生态足迹。

全部细节在我们的[技术报告](https://arxiv.org/abs/1911.04124)中。

我们感谢莎拉·艾伦帮助我们编写了这篇文章。

[![](img/a06b758bdcc47dca7c2504f298674d87.png)](https://coincodecap.com)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)