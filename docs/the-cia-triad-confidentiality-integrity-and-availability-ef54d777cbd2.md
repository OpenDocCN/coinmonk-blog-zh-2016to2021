# 中央情报局的三位一体:保密性，完整性和可用性

> 原文：<https://medium.com/coinmonks/the-cia-triad-confidentiality-integrity-and-availability-ef54d777cbd2?source=collection_archive---------1----------------------->

![](img/69e99a51682c226019c56d32edb19636.png)

Photo by [said alamri](https://unsplash.com/@saidalamri63?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

鉴于脸书目前面临的可用性问题，让我们一起来了解一下网络安全的基本参数。当你阅读更多关于安全概念、政策等的内容时，你会发现很多关于 CIA 三合会的参考资料。这与美国代理机构无关。这是讨论网络安全时应该始终牢记的一个基本概念。你可能觉得它太基础了，但它非常重要。CIA 代表机密性、完整性和可用性。为什么这三个字这么重要？因为它们定义了您的安全目标。安全控制措施通常是根据它们如何解决这一问题来评估的。

所以让我们深入了解一下中情局的定义。

# 保密性:

我们都知道保密的定义是保持某件事情秘密或私人的状态。在网络安全术语中，它描述了防止或最大限度地减少对我们想要保护的一些数据的未经授权的访问的目的。这是如何实现的？通过确保只有目标用户有权访问被访问的资源。实现这一点的一些常见方法有:加密、访问控制、职责分离、双因素身份验证等。在网络上，数据可能处于不同的状态:存储中、传输中或处理中。必须实施机密性控制来保护所有状态的数据。当然，根据数据的状态，需要不同的安全控制。

有许多攻击可以获取未经授权的数据，我在下表中列出了一些可能的对策:

![](img/d82c8fac198d3cd6d7c26f75b41d191a.png)![](img/eddc45c8ad6bced8de8efff881cbe3e2.png)

Example of a Man-in-the-Middle attack where the attacker intercepts Alice’s messages and send them to Bob. Alice and Bob think they are communicating with one another. However, the attacker is manipulating the communication.

# 完整性:

保护数据的完整性意味着只允许对数据进行授权的修改。换句话说，它确保数据保持正确和可靠，同时允许授权的更改。只有授权用户才能修改数据。不仅对象本身不应该被改变，管理和操作它的工具也不应该被破坏。

诚信有两个方面需要关注:

*   不应允许未经授权的用户更改数据
*   不应允许授权用户对数据进行未经授权的更改

在上面提到的所有数据状态中都应该保持完整性。必须实施安全控制以保持完整性，例如:访问控制、数据验证、数据备份等。

诚信依赖于不可否认性。这意味着没有人能否认一个事件已经发生。因此，如果任何人访问，修改或删除一个对象，他们不能否认他们的行动。这只能通过适当的识别、认证、授权、问责和审计来实现。为此，保留访问和操作日志至关重要。正如您所知道的或即将发现的，日志在网络安全中非常重要。

完整性破坏可能由以下原因引起:

*   文件修改
*   文件删除
*   改变配置
*   错误配置的安全控制
*   恶意软件，如病毒、逻辑炸弹、特洛伊木马

为了对抗完整性攻击，建议实施良好的访问控制和身份验证程序以及入侵检测系统(IDS)、哈希总量验证等。

# 可用性:

最后一个原则是可用性，顾名思义，它指的是不间断地访问对象。因此，可用性与可靠性和系统正常运行时间密切相关。确保可用性的安全机制可以高度可靠地防止拒绝服务攻击。可用性还要求支持基础设施(如网络服务和通信)不间断运行，允许授权用户访问授权资源、对象或数据。

在这种情况下，安全控制必须提供良好的标识和身份验证，以确保授权访问。此外，必须提供良好的性能水平。必须快速处理中断，以确保冗余、可靠的备份，并防止数据丢失和破坏。应准备好备份数据、灾难恢复和监控，以降低不可用的风险。

下表列出了一些攻击可用性的示例:

![](img/f7784d4671884362215d0b2468d7395b.png)

机密性、完整性和可用性可能会被非恶意问题破坏，例如硬件故障、人为错误或自然灾害。大多数违规是由非恶意问题引起的。在设计和实现安全控制时，应该记住这一点。

# 保密性、完整性、可用性:你的首要任务是什么？

我们已经定义了构成中情局三人组的三个词。现在，下一步是确定您的安全目标。每个组织在执行中情局原则时都有自己的优先事项。提供服务的公司必须确保其产品始终可供用户使用。对于一些人来说，这可能是一个优先事项。其他组织可能更倾向于保密，如政府机构。一个好的安全策略需要很好地理解组织的目标。为什么？因此，实现的机制提供了安全性，同时允许实现业务目标。

在另一篇文章中，我向你介绍了两种常见的型号，Biba 和 Bell Lapadulla。Biba 是注重诚信的典范，Bell-LaPadula 则注重保密。这些模型描述了不同主体和客体之间的信息流。如果你有兴趣了解更多，我邀请你阅读这篇文章。

如果您有任何问题，请随时联系我们！

你的安全，

克里斯特尔·费加利

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 也阅读

*   [最佳免费加密信号](https://blog.coincodecap.com/free-crypto-signals) | [YoBit 评论](/coinmonks/yobit-review-175464162c62) | [Bitbns 评论](/coinmonks/bitbns-review-38256a07e161)
*   [如何购买 Monero](https://blog.coincodecap.com/buy-monero) | [IDEX 评论](https://blog.coincodecap.com/idex-review) | [BitKan 交易机器人](https://blog.coincodecap.com/bitkan-trading-bot)
*   [币安 vs 比特邮票](https://blog.coincodecap.com/binance-vs-bitstamp) | [比特熊猫 vs 比特币基地 vs Coinsbit](https://blog.coincodecap.com/bitpanda-coinbase-coinsbit)
*   [如何购买 Ripple (XRP)](https://blog.coincodecap.com/buy-ripple-india) | [非洲最好的加密交易所](https://blog.coincodecap.com/crypto-exchange-africa)