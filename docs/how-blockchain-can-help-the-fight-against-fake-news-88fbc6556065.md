# 区块链如何帮助打击假新闻

> 原文：<https://medium.com/coinmonks/how-blockchain-can-help-the-fight-against-fake-news-88fbc6556065?source=collection_archive---------5----------------------->

我知道，我知道——几乎所有的帖子和谈话都试图让你相信，这项技术是一种神奇的东西，可以解决所有可能的问题。但如果我们以区块链的基本属性为例，在互不信任的各方之间加强信任，暂时忘记加密货币，那么，肯定会有区块链成为工具的情况。

因为我尊重你的智慧，我不会试图给假新闻下定义，也不会解释为什么它们是有害的，但在此之前，让我们先问问假新闻是如何传播的。传统的答案是由一些组织甚至政府支持的巨魔和人工智能，但这是可能的来源，而不是假新闻传播的媒介。媒介是人类，或者更确切地说，是人类的欲望、愤怒、憎恨和遗憾。人们总是会相信与他们内在的、非理性的、有时甚至是荒谬的信念相共鸣的“新闻”。你讨厌一个受欢迎的人，内心希望一些不好的事情发生在他身上？当你看到一个关于这个人因为一些非法活动而被逮捕的“新闻”时，你会有什么反应？这是问题的核心，社会媒体，抱着每个观点都一样的态度，只是催化剂。是的，区块链不能帮助“修复”人类心理。区块链可以通过降低这种催化剂的强度来提供帮助。

那么可能的解决方案是什么呢？一个是所有的新闻都要得到某个权威的“批准”，是的，你是对的，这是个可怕的想法。审查制度可能非常有效，但会造成许多不良的副作用。

另一个解决方案不需要审查，它需要一个能够证明新闻来源的机制。仅此而已。那么这是如何工作的呢？

让我们假设有一个区块链(没有密码，没有硬币),主要新闻机构在其上注册他们将流向其同事和合作伙伴的新闻，并且每个新闻都收到一个唯一的 ID，该 ID 由区块链生成并与该特定新闻相关联，并且该 ID 被添加到新闻本身中。如果是数字形式，可以使用人类看不见但计算机可读的页眉，如果是打印的，则可以在页脚中打印 ID。这种类型的头现在被普遍使用，并且实现起来非常简单。

每个人都可以核实消息的来源和有效性，并决定他/她是否信任这个来源，如果他/她愿意的话。因为 ID 可以被计算机读取，所以可以开发浏览器扩展来自动进行这种检查，并为用户提供一些关于来源、有效性或类似特征的简单视觉反馈。此外，该扩展还可以添加定制、个人偏好甚至个人评级的功能。在一些系统中，未经核实的新闻可能会被完全隐藏，但这是因为用户决定这样做，而不是因为一些中央机构对假新闻有灵活的定义。这不是“用户评级”的内容，这只是一个跟踪新闻来源的工具。

新闻的注册完全是可选的。严肃的专业新闻机构会有动力去登记他们的新闻，那些制造假新闻的机构不会这样做，因为很快，即使不需要大数据分析，也会清楚这个机构不是新闻机构，然后可以采取行动。许多国家都有非常严厉的法律禁止假新闻。

因此，严肃和尊重的机构将标记新闻，并要求他们的合作伙伴和同事也重新分发它们。假新闻不会。分享和发布新闻的各方都希望添加这个 ID，因为如果没有这个 ID，新闻将很少有人知道，而且一般来说，人们会非常乐意限制“点击字节”和假新闻的数量。

但是，这将如何有助于打击在特定人群中放置虚假信息的网络钓鱼者呢？它会阻止传播，新闻会留在所谓的“回音室”里。

此外，这个过程不歧视组织的大小。小型区域机构将拥有与大型机构相同的访问权限和功能，不需要额外的成本，并且该过程对于技术实现来说是微不足道的。

但是，如果有人利用几条新闻来创建一个分析或类似的呢？他/她可以添加所用新闻的 ID 并生成新的 ID。

但是，如果社交媒体开始使用这些 id 进行审查或锁定目标，那该怎么办呢？是什么阻止他们现在做这件事？这种方法的概念是让用户自己决定，而不是让别人替用户决定。

如果有人在散布假新闻，却用 ID 做真实的新闻呢？ID 与实际新闻和实际文本相关联。即使对计算机来说，两个文本的语义比较也很容易。

但是有理智的人能够自己决定，这有什么区别呢？很简单:

> 如果一个谎言重复的次数足够多，它就会变成真理——约瑟夫·戈培尔

没有办法阻止不理智的人停止不理智的行为，但是有一种方法可以帮助理智的人保持理智——允许他们检查信息。

该项目的实际技术实现并不复杂，因为区块链是按设计分布的，节点可以由任何感兴趣的团体、非政府组织、新闻机构、社会媒体和个人托管。有很多平台可以使用，Hyperledger Fabric 就是其中之一。以太坊并不合适，因为存在可伸缩性问题、可放入数据的限制以及存储数据的相关成本。

这会有用吗？是的，如果新闻机构和个人希望有所改变。如果情况并非如此，那么政府必须增加额外的激励措施，但这是一条非常危险的道路。