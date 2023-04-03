# 代币经济学:当代币击败股票

> 原文：<https://medium.com/coinmonks/tokenomics-when-tokens-beat-equity-aa4c503bc5bd?source=collection_archive---------1----------------------->

![](img/8fe5b62835d03ae639d20aae7c3f735a.png)

在过去的两年里，初创企业的融资有了惊人的发展。2018 年，超过 900 家早期实体通过发行区块链代币或硬币筹集了约[217 亿美元](https://www.coinschedule.com/stats.html)，同时绕过了大多数传统金融业。

为了理解区块链资本市场的这种繁荣是多么非同寻常，让我们看看加拿大 TSX 风险投资公司(TSXV)，这是一个为初创企业建立的非常成功的市场。在这个公开市场上市的 1，400 家公司的总市值为 410 亿美元，这是在 20 多年的时间里建立起来的。2018 年，TSXV 仅进行了 8 次 IPO，总融资额约为 0.4 亿美元。

尽管筹集了大量资金，而且人们对这些代币有明显的兴趣，但大多数金融业内人士对代币和硬币作为融资工具深表怀疑。有很多欺诈行为、见不得人的生意、把钱扔出窗外的公司——这是一个真正混乱的环境。被噪音淹没的是一场关于代币概念的经济可行性的严肃辩论。最大的问题是:**代币融资有经济价值吗？代币能解决监管套利以外的问题吗？**

在这篇文章中，我想提供一个简短的总结(嗯，像一个学者一样简单——就像我 8 岁的孩子说的:爸爸，我问你一个简单的问题，不要给我一个完整的答案！)最近的一篇[论文](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3286825)，我和我的合著者 Katya Malinova 写了这篇论文来回答这些问题。

**一些背景:**在首次硬币发行(ICO)中，投资者通常以加密货币的形式向发行者转移资金，以换取基于区块链的代币或硬币。这些令牌是一段段代码，“智能合约”，以分散的形式存储在定制的区块链上，或者在以太坊这样的成熟平台上运行。

原则上，区块链或加密令牌可以承担许多经济功能。例如，它们可能代表传统资产，如对发行者的债务或股权要求，或对实物资产的要求(这是最新的东西:安全令牌——但这不是我在这里写的！).我们研究的是更常见的形式，即令牌通过基于区块链的基础设施向其持有者提供对应用程序或服务的访问，并且不转移所有权。让我们将它们广义地定义为实用令牌。

代币销售可以筹集数十亿美元，而且它们有一些吸引人的次要特征，比如在二级市场上的可交易性。然而，我交谈过的每个人都很难理解这些代币在经济上到底是什么，以及出售它们是否有任何内在的 T2 经济价值(超出监管和交易成本套利)。当一种新的投资产品出现时，关键的问题是:这种产品是否具有*经济*功能，使原本不可能的投资成为可能？

在我们开始之前，让我强调一下，这是一个需要理论的问题——我知道每个人都喜欢数据，对人工智能和机器学习感到兴奋。但这是一个基本问题——计算机不会给我们答案！

我们的论文: Katya 和我开发了一个理论模型，并表明一个设计良好的代币发行合同可以降低非常标准的内在代理成本，这种成本源于发行者和投资者之间的利益冲突。因此，代币可以用来成功地为无法获得股权融资的项目融资。

我们的工作不存在于真空中——我们在同事的一些非常有见地的工作基础上进行构建和扩展。麻省理工学院的 Christian Catelini 和罗特曼的 Josh Gans 今年早些时候分发了一篇非常有见地的文章，他们在文章中认为典型的代币销售是未来收入的预售。它们提供了第一个框架，表明代币如何用于发行基金，以及代币发行者的“货币政策”——发行未来代币——如何影响投资者提供基金的意愿。很酷的东西，我强烈推荐阅读。

另一个伟大的作品来自波士顿学院和波士顿大学的 Jiri Chod 和 Evgeny Lyandres:他们将象征性销售建模为公司未来收入的一部分的预售，他们表明这种 ICO 融资方法导致相对于风险资本融资的投资不足。

我们以 Josh、Christian、Jiri 和 Evgeny 的工作为基础，将代币发行作为股权融资的替代工具，为追求利润最大化的企业家所用。人们可能对其他事情感兴趣，但我们不感兴趣(至少在本文中不感兴趣):例如，我们不关注加密货币在平台采用中的可能作用。

在我们的模型中，垄断企业家有一个提供特定商品或服务的创业想法，她需要启动资金来建立提供这种商品或服务的平台。这种商品的需求是不确定的，只有在发生了设立成本，搭建了平台之后才会实现。在我们的基线模型中，需求分布是外生的；具体来说，我们假设一个截距不确定的线性反向需求。(我知道这听起来太简单了，但好的经济理论的关键是节俭——只包括你必须的东西，尽可能简单而不简单化。)

企业家可以从投资者那里筹集资金，交换股权，或者交换代币，代币持有者有权获得未来产出的预定份额。和早期公司一样，我们假设发行人无法进入信贷市场(给我看看能获得银行贷款的初创公司！).

在我们的模型中，投资者对融资方式没有偏好，只要他们在预期中收支平衡，他们就会为企业融资。(这只是为了简单起见:你可以包括一个要求的回报率，这不会改变任何事情，但会使事情变得更加混乱。)

在时间 0，企业家决定融资的选择，在时间 1，一旦初始投资已经完成，她学习需求函数并选择利润最大化的数量。在我们的基线模型中，发行者选择利润最大化的产品进行股权融资，所有盈利的项目都可以通过股权融资。

对于 ICO 融资，发行者可以选择如何组织发行，我们表明这种选择是至关重要的，因为它会影响企业的盈利能力。首先，企业家可以发行代币来换取未来收入的一小部分*和*。机械地说，创业者会在零时间发行 *T* 代币，前期制作，把这些的 *t* = *a*T* 卖给投资人，并承诺以后不发行代币。然后，投资者将准确地收到收入的一小部分 *a* 并且当总共生产出 *q* 单位的产出时，每单位的商品或服务需要 *T/q* 代币。

这种融资方法，我们称之为*收入分享*，相对于利润最大化的数量，导致“生产不足”(类似于 Jiri Chod 和 Evgeny Lyandre 工作中的投资不足)。为什么？生产激励被扭曲了，因为企业家生产的数量使得她的边际收益份额等于全部边际成本，而这个数量低于最优数量。

收入分成并不是安排象征性销售的唯一方式:第二个选择是预售一定数量的 T2 产品。这实际上在众筹中很常见——想想 Kickstarter，你在那里预付你的四轴飞行器。从机制上来说，企业家将在零点、生产开始前的*时间发行 *t* 代币，将这些代币卖给投资者，并预先承诺固定的代币对产出的汇率。*

在这种情况下，企业家不事先承诺将要发行的代币的总数，并且将在已经做出生产决策 *q* 之后向她自己发行 *q-t* 代币。这种融资方式，我们称之为*预售*，一般会导致一种非常不同类型的代理冲突。当选择最优产量时，企业家未能将生产额外单位产量对令牌持有者的价格影响内在化。“未能内化”是“哎呦——对不起，我给你带来了一些痛苦，但这对我更好”的经济学说法(这意味着，由于无法承诺，我最终也会受到伤害)。相对于使企业总利润最大化的数量，这种外部性导致“过度生产”。

那么我们学到了什么？到目前为止，我们的讨论表明，选择 ICO 融资对融资回合的成功至关重要。两种类型的 ICO 融资方法都导致生产数量不能最大化项目的预期净现值。因此，即使没有其他摩擦，也会有盈利项目无法通过这两种象征性方法中的任何一种进行融资。

现在怎么办？好吧，直觉上，如果一种方法导致生产过剩，而另一种导致生产不足，我们能不能把两者结合起来做得更好？你猜对了，我们可以，正确的组合让我们回到最佳状态。怎么做，为什么？好吧，很多人可能不喜欢这种说法，但基本想法是征收一种税——不是政府税，而是像税收一样运作的合同付款。以下是方法。

当我们向代币持有人预售固定数量的未来产品时，代币持有人会遭受“外部性”(抱歉效应)。纠正外部性的传统方法是征税。在这种情况下，通过适当的动态代币*货币政策* : **除了向投资者出售一组初始代币(代表对产出单位的要求权)，发行者还承诺向投资者提供在生产决策**(=税收)后*发行的额外代币的收入份额。*

换句话说，最优结果是一个*动态代币合同*，它巧妙地将 ***的产量预售和*** 的未来收益分享结合起来。在我们的模型中，这总是可能的，并且有一种独特的方式来实现。如果做得正确，这个最优契约确保企业家对生产数量的选择使企业的总利润最大化，然后所有可以通过股权融资的项目也可以通过 ICO 发行融资，反之亦然。

现实中能做到吗？我看不出有何不可——如果不是这样，智能合同还有什么用？！

还有一件事要提一下。早期的代币发行限制代币的数量，并将其作为一种美德。这并不是因为发行者可能已经错过了有利可图的机会(并剥夺了感兴趣的客户/平台用户！).同样，我们的模型提供了如何正确操作的指导:预售一些代币，并在以后生产更多代币时分享收益。

虽然我们认为代币和股权的等价本身很酷(所以代币本质上并不劣于股权)，但当我们想到企业的成功取决于企业家不可观察、不可验证和不可契约的努力时，这变得非常有趣。是的，创业者想做好，有上进心。但是投资者真的知道吗？投资者能监督 CEO 在做什么吗？传统的风险投资和天使投资可能在帮助公司驾驭“正常”的技术世界方面做得很好，但在这个新经济中，他们可能力不从心，特别是当发行人本身是“分散的”时。谁能监控分散在世界各地的开发团队？(大多数风投甚至不喜欢与不在旧金山湾区的创始人打交道。)这种类型的*道德风险*当然应该是首要关注的问题。

事实证明，在这种企业家道德风险的情况下，代币优于股权！我们发现，由于企业家的道德风险，代币融资和股权融资之间的利润对等被打破，代币，而不是股权，为企业家提供了更强的激励！

这是如何工作的？关键机制是，在最优动态契约下，只有当需求足够高(更多努力= >更多需求)时，发行者才能获得正利润。当道德风险较弱时，任何可以用股权融资的东西也可以用代币融资。但是，当道德风险严重时，发行者只在象征性契约下努力(不努力她什么也得不到)，她不会用股权来做这件事(无论如何她都有所收获)。因此，所有可以通过股权融资的盈利项目也可以通过代币融资，但是一些可以通过代币融资的盈利项目*不能通过股权融资*。

我认为这个结果非常好，但是不可否认的是，这个机制有种似曾相识的感觉。问题在于，最优代币发行合同有一种债务融资的味道，因为当一家公司有债务时，发行者在需求较低时不会得到报酬，因此有强烈的动机去努力。债务有助于抵消道德风险，这是 70 年代詹森和梅克林的开创性工作中已经提出的观点。事实上，杠杆收购的大部分业务都是基于这种想法。

下一步是什么？个人希望监管者退一步。显然有人愿意把钱投入这些实体的代币。购买区块链代币很麻烦，需要很多步骤——这不像你在 TD Ameritrade 屏幕上就能买到。这些投资者不可能被骗去相信这是一家普通的纽约证券交易所上市公司。你的金融机构，事实上，让你很难订婚。保姆是怎么回事？(作为一个自豪而谦逊的加拿大人，我想说，我的印象是，证监会比其他一些监管机构更加开明！)

政策制定者和监管者应该发挥作用:他们应该寻求制定规则和机制，让发行者和投资者能够在安全的环境下进行互动，并确保投资者在参与金融活动时能够获得关于代币经济功能的正确信息。我的感觉是目前的法律是不够的。正如我们在论文中指出的，代币在风险融资中发挥着股权无法发挥的作用。对代币发行设置 IPO 级别的障碍，对于早期企业来说简直是不可逾越的，这可能会损害经济上可行的实体，并损害经济增长。

总之，**代币发行在概念上可能是过去十年公司融资中最大的创新。**我们的工作为未来基于收入的代币发行提供了一个蓝图:代币持有者的最佳补偿应该包括产出单位的预售和未来分享收入的承诺。**通过最优代币合同，代币可以像传统股权一样为相同的项目融资。在我们模型的背景下，代币甚至更优越，因为它们可以为一些无法用股权融资的盈利项目融资。**

**全文可以在这里找到:[https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3286825](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3286825)**

**更新(11 月 24 日):我在 YouTube 上创建了一个演示幻灯片的叙述版本(约 19 分钟):[https://www.youtube.com/watch?v=52giUWmgxN0&t = 6s](https://www.youtube.com/watch?v=52giUWmgxN0&t=6s)幻灯片在这里可以看到:[https://slides.com/ap248/tokenomics](https://slides.com/ap248/tokenomics)
我还对文本的结构做了一些小的改动。**

> **[直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)**

**[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)**