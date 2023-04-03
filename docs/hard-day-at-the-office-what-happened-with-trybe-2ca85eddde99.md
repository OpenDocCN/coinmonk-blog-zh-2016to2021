# 在办公室辛苦的一天 TRYBE 怎么了？

> 原文：<https://medium.com/coinmonks/hard-day-at-the-office-what-happened-with-trybe-2ca85eddde99?source=collection_archive---------3----------------------->

![](img/ce05c2bfe29f88af9b308f5c652005dc.png)

Look at all that fire

区块链是一个非常有趣的空间，没有隐藏。这才是重点，对吗？当你把事情搞砸的时候你会怎么做？通常你让痕迹消失在你有围墙的花园后面。IT 专业人员团队和客户服务门户网站纠正了错误，打电话的人很高兴，而整个世界却一无所知。

当你在区块链上搞砸了，每个人都有机会看到哪里出了问题。他们可以选择指指点点，开怀大笑，也可以从别人的不幸中吸取教训，让整个系统变得更加强大。

在我对 EOS 进行哲学思考之前，到底哪里出了问题？

## 发生了什么事？

TRYBE 提供了一个独特的方案，用户可以在部署 TRYBE 令牌之前在他们的站点上注册。一旦令牌被部署，公众可以空中抓取令牌。TRYBE 在其网站上兑现代币，并为注册预代币和抢票提供奖金。

因为用户可以提供任何他们希望不总是有效的名字。

*   经验教训:如果可能的话，对于外链站点…确定一个链接真实 EOS 账户和外链身份的方法。如果由于某种原因这是不可能的，也许用户的不便被认为是太高了，在验证 EOS 名称时要更加小心。

airdropping 实用程序使用 eosjs，这是第一个也是最常用的 javascript 库，用于与 BlockONE 提供的 EOS 区块链进行交互。在 6 周前的一次空投的初始测试中，确认了一个关键配置标志`broadcast: false`来阻止事务发送。

在测试所提供名称的过程中，该标志被保存在`broadcast: false`中，然而不知何故，这些交易显然已经被广播了。

> ***更新:*** *看来在当前版本的 eojs*`*eos.transaction(...actions)*`*中忽略了某些配置设置。
> 要绝对强制，你需要使用*

*   经验教训:不要相信软件包在升级时不会改变或总是以同样的方式工作。即使您确信代码可以工作，也要执行本地测试来确保它。

当识别出代币的错误转移时，停止该过程并评估解决方案。

## 是怎么解决的？

1.  冻结令牌的传输，以防止无效的丢弃数量移动并变得不可管理。这是通过给 transfers 动作添加一个`eosio_assert(false,"no transfers allowed")`快速完成的。代码被编译和部署。
2.  代币冻结后，需要格外小心。转移被进一步修改，只允许空投者转移代币，并增加了一个`airdropfix`动作，允许在不改变总供给的情况下返还空投的资金。这一决定被认为是可以接受的，因为到目前为止，所有的代币都是预先给亲密支持者的礼物。这也将公开披露冲销交易，而不是隐藏它们(这是一个可用的选项)。**在回应中，透明度总是被认为是最重要的**。
3.  在丢弃令牌值之前和之后，拍摄所有参与者的快照。因为从无效丢弃到检测到的时间很短，并且仅影响将被丢弃到的近 10，000 个帐户中的大约 120 个，所以通过其他方式获得或被转移的令牌的风险是可以接受的低。
4.  使用`airdropfix`操作检索无效令牌。
5.  应用了正确的空投分配，授予了近 10，000 个帐户令牌。
6.  最初的智能合同已经部署，取消了转会冻结和`airdropfix`功能。

TRYBE 团队在整个过程中与社区的沟通非常出色。

![](img/1085fed8fd505694c057daf231a9c85f.png)

Team work and community held the chain together!

## 这教会了我们什么？

EOS 的一个简单事实是，不像它之前的许多区块链，值是可变的——这意味着它们可以被改变。对于那些主要诉求是一枚硬币或代币是不可逆转的，永远是你的…或永远失去的…那么在 EOS 上有一些方法可以做到这一点，但它们不会被普遍采用。

EOS 创造了一个新的空间，在那里你采取的行动将被永远记录下来，只要积木被制作出来，所有人都可以看到，但它也是一个当你的错误与你的意图不符时，你可以修复错误的地方。

许多 DAPPS、行业、政府可能正因为这个原因而关注 EOSIO，没有僵化的问责制。TRYBE token 是一个为知识共享提供动力的工具。代币必须尊重它为每个想要信任和参与社区的人制定的规则，即使这意味着暂时离开以纠正错误。

最核心的是，EOS 需要一些信任，而信任的回报是透明。

## 如果我不想信任呢？

这就是 DAC 的威力变得重要的地方。分散对易变事物的控制将成为信任易变的关键。当整个社区都能参与到决策过程中来，创造美好的事物或修复出错的事物，他们就更有可能信任这些决策。

虽然比独狼式的快速决策或小型委员会慢，但它提供了一个强大的保障和声明，说明你们正在一起做的事情，以及对其可靠性的信心。

## 你怎么想呢?

EOS 正在开辟新的领域，像这样的事件应该被用来考虑技术和社区的发展。人类会犯错误，我们有义务从中吸取教训，让事情变得更好。

*   一项技术应该惩罚我们的错误并阻止我们去修复它们吗？这实现了什么？
*   太多的灵活性和自由是否会侵蚀信心和减缓采用速度？还是透明的灵活性是广泛使用的缺失环节？
*   有中间立场吗？它看起来像什么？

社区和技术需要找到这些答案，这样它才能继续发展，成为我们都希望看到的改变世界的力量。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)