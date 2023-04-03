# 相信我！建立一个区块链/DLT 财团并不容易

> 原文：<https://medium.com/coinmonks/trust-me-its-not-easy-to-build-a-blockchain-dlt-consortium-d94d5bb32b9d?source=collection_archive---------0----------------------->

![](img/42257585a53edb0fba0bc9601c2091dd.png)

Photo by [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

本周我看到了 R3 Corda 发布其 [Corda 网络](https://corda.network/#)的公告。什么是科达网络？这是一个由网络参与者运营的公开的 Corda 节点互联网。[……]该网络于 2018 年 12 月上线，目前由 R3 管理。**一个独立的非营利基金会已经成立，以管理该网络，**一个由参与者组成的过渡委员会将于 2019 年春天选举产生，该委员会将监督该基金会，直到一年后举行民主选举。详见[治理模式](https://corda.network/governance/governance-guidelines.html)。

从这个简短的描述中，你可能会想:*“哇，用许可的 DLT 技术(如 Corda)来构建一个公共可用的网络一定是一个真正的技术挑战。它一定会引起一些可伸缩性和操作问题”。*但事实并非如此，**使用私有/许可技术构建这种分散式网络最困难的部分是其治理。**网络的治理及其负责的母财团或政治组织。

在建立一个区块链/DLT 财团时，我们一定会发现什么样的摩擦和困难？在这篇文章**中，我将试着与你分享你在建立自己的区块链财团时(很可能)会面临的主要复杂问题**

*(免责声明:此分析是我个人经验和对板块持续观察的结果。不要把它作为一个彻底的分析，而是作为一个友好的指导方针，就像你在酒吧里从一个对区块链略知一二的朋友那里得到的那样。*

***确定财团类型:*** 第一个大问题是确定我们要建立什么类型的财团。为了决定这一点，我们必须与其他创始成员讨论，我们希望在我们的网络上构建什么样的用例。我们是否要在创始成员之间构建一个特定的用例，并且不需要其他成员加入？在这种情况下，我们将在双方之间建立一个私人财团。或者，我们是否更有雄心，想要建立一个行业联盟或公开联盟，任何满足一组基本要求的人都可以要求加入并与网络的任何其他成员一起构建自己的用例/应用？财团应寻求建立半公共/许可财团的案例。

![](img/6b997a1e6eef5c59584f8bce8868dd22.png)

Photo by [Clint Adair](https://unsplash.com/@clintadair?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

***确定联合体成员:*** 一旦我们作为创始成员，决定了我们想要建立的网络类型，我们就必须决定我们将接受什么类型的成员加入网络。我们只接受大公司参加我们的聚会吗？潜在的新成员需要由治理实体投票决定吗？我们是否要将加入成员限制在特定的部门或活动领域？**对该财团来说，一个明确的使命肯定有助于解决这个难题。**

***确定成员类型/角色:*** 这是个乱七八糟的。成员可以在联盟中扮演不同的角色:他们可以作为治理实体的**成员加入，因此他们在联盟技术/政治决策、战略和路线图中拥有投票权；**正式成员，**具有在网络上部署他们的应用程序、查询和存储分类帐中的数据的全部特权，或者甚至参与共识和验证算法(取决于底层的区块链技术，这可能意味着具有部署他们自己的验证节点的许可)；**部分成员，**允许部署应用程序和查询存储在账本中的任何信息，但没有验证权限；**只读成员，**如监管者，只拥有查询分类账中存储信息的权限；或者**用户，**哪些是会员允许使用其应用的最终用户。决定允许不同的加入实体成为哪种类型的成员可能是产生摩擦的一个重要原因。**

在联合体基金会中，**明确说明一个加入成员需要成为各类成员的具体要求**(根据联合体和创始成员的具体目标)**非常重要，以避免将来因优惠待遇而产生冲突**。想象一下，你将治理实体的席位限制为创始成员，当像 FAANG 这样的大公司想要加入财团时，你在治理实体中给他一个席位，即使他不是创始成员，其余的成员可能会被冒犯，并不信任该组织的“去中心化治理”。

***联合体的政策、法规和章程:*** 加入联合体时，所有创始方及加入方都必须签订合同，合同中明确规定并明确接受联合体的主要规章制度。在这份合同中，我们将包括诸如成员类型、加入成员要求等内容。构建和撰写这份合同并不是一件容易的事情，它意味着(至少)创始成员的法律部门之间的持续斗争。此外，在本合同中，需要定义联合体的法律实体。**我们会成为一个非政府组织吗？我们会合并这个财团吗？如果是，谁被允许参与公司？**

***联合体技术基础设施的运营、管理、成本和 SLA:***根据法律规定，必须确定技术基础设施的运营和管理模式。谁将负责网络运营成本，如何负责？如果网络需要升级或新的开发，谁来开发并最终支付费用？又是怎么做到的？为了使这个联盟正式化，在其基础上，**必须决定网络背后的商业模式。我们是否要建立一个营利性公司**(类似于服务提供商公司)，由负责维护网络、收取使用费和支付底层基础设施费用的联盟的每个成员参与？利润会再投资到网络中还是在参与者之间分配？**还是我们愿意将每个节点的维护委托给它的所有者，**这样每个成员都必须承担成本并确保它不会失败，以保证相应的 SLA？如果成员国未能履行其义务，会发生什么情况？谈到 SLA，我们应该如何决定网络的具体 SLA？我们必须牢记用于网络的底层 DLT 技术，以及它的特定使命和用途。您可能会发现，所有这些都可能成为组织冲突的巨大来源。

此外，如果我们考虑营利/服务提供商的选择，**会员使用网络将如何收费？在现收现付的基础上**所以你做的交易越多，你将被收取越多的费用(使用汽油或等效的公用事业代币)？**每位会员都将被收取年费**，以获得网络使用权？或者，我们是否应该更进一步，**不仅对网络中使用的交易和资源收费，还对访问存储在总账中的数据收费**(因为它可能是具有大量商业价值的数据)？最后，我们会根据一个实体在财团中的成员类型收取不同的费用吗？好的问题，需要解决之前，我们跳盲目折叠到财团的建设。

![](img/3d691396291a00c467e13c6b437a19f1.png)

Photo by [Helloquence](https://unsplash.com/@helloquence?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

***由联合体建立的技术和网络的知识产权:*** 最后但同样重要的是，谁拥有联合体成员为网络所做开发的知识产权？它们应该属于所有的参与者(如果适用，共享版税)，还是只属于创始成员，或者每个新的开发都应该开源以避免冲突？很明显，应用程序和特定用例属于开发它们的参与者，即使它们部署在网络中并由几个参与者共享。然而，一旦这些发展成为核心网络的一部分，情况就不那么清楚了。

解决这个问题的一个好方法是定义**网络中的两个明确规定的层:协作层，**其中每个新的开发都专注于增强网络并使所有参与者受益，这样他们的知识产权属于联盟及其成员(因此他们可以是开源的、共享的或任何决定的)；**竞争层，**联盟成员竞争在网络上部署他们自己的应用程序并提供给他们的客户，这是这些开发的知识产权，显然是其开发者的财产。

检测到所有这些摩擦后，**是否可以利用区块链技术和智能合同的优势来解决所有这些摩擦，以便通过联盟网络中的一组智能合同**来正式制定和执行已决定的规则和政策？这是阿拉贡人正在通过他的道探索的事情。就我个人而言，我认为这并不容易，但我邀请您深入了解这个有趣的去中心化项目。

我希望这个快速的知识药丸能让你明白建立一个区块链财团并不是说起来容易做起来难。为了不写太多，在我的第一个版本中，我包含了上面分析的每个摩擦的具体例子。如果你有兴趣知道更多关于这个问题的信息，请不要犹豫，联系我或给我留言。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)