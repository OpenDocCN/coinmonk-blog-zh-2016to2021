# 从 ETHSecurity 社区学到的东西

> 原文：<https://medium.com/coinmonks/learnings-from-the-ethsecurity-community-57431ae0ed5e?source=collection_archive---------4----------------------->

![](img/c630657ec0fad7c7d72e2747e375e54e.png)

ETHSecurity 社区旨在鼓励和支持围绕以太坊生态系统安全主题的协作、研究和教育。它通过管理知识库、促进交流、组织会议和领导所需社区项目的工作组来做到这一点。该社区已经拥有 170 多名成员，并继续受到个人和行业机构的青睐。

构建安全的 web 应用程序很困难。构建安全、智能的契约驱动的分散式应用程序需要全新的编程范式。随着以太坊生态系统的不断扩展和欢迎许多新的开发者，有必要为他们提供资源、流程和工具，引导他们构建安全、分散的应用程序。

## 一些历史

ETHSecurity 于 5 月下旬推出，当时 [Robbie Bent](/@robbiebent) 在经历了 Truebit 令人沮丧的智能合同审计流程和 ETHPrize 成功的[社区面试流程后，邀请我帮助他复制 ETHSecurity 的面试流程。目标是创建一个拥有安全领域最大参与者的强大社区，阐明该领域所需的工具和项目，为这些项目提供资助，并向社区的通用指导原则或标准迈进。我们第一次采访的是模块化公司的](https://our.status.im/tag/ethprize/)[克里斯托弗·布朗](/@hackdomETH)，他随后加入了我们的采访和组织工作。

从那时起，ETHSecurity 已经与一流的智能合同审计公司、opsec 专家、安全研究人员、赏金猎人和复杂的 DAPP 开发人员进行了总共 29 次社区访谈。通过这些采访，我们在一个由安全专业人士组成的电报小组(在撰写本文时有 172 名成员)中播下了对话的种子，并开始为 9 月 6 日在柏林举行的安全会议准备内容。

一路上，志愿者们贡献了数百个工时来产生动力。最引人注目的是，ETHSecurity 与 SecurETH 的 [Bryant Eisenbach](/@fubuloubu) 、 [Rex Hygate](/@rex2) 和[Roman pavlovsky](/@rpavlovs)合作，他们在 Maurelian 的建议下开始了 unconference [的基础工作。](https://ethereum-magicians.org/t/proposed-gathering-of-the-security-community/356)他们的工作有助于引导 unconference 并在 ETHSecurity 社区中建立巨大的影响力。

ETHSecurity 也获得了以太坊社区基金(Ethereum Community Fund)的资助，以支持采访过程、本报告的创作以及到目前为止通过 DevCon 进行的社区建设。他们的努力令人难以置信，我们很高兴能继续携手共创未来。

security unconference 被超额订阅，达到了最大容量，最终有 90 多名与会者参加了 8 场关于生态系统安全现状的闪电讲座，以及 8 场研讨会。研讨会主题包括:

*   [社区结构和组织](https://docs.google.com/presentation/d/1GEsy2iKCs-OTJqp-glvQ2KSMWo4hZSAaTvgx7joWpXI/edit?usp=sharing)
*   [安全资源的集中式 wiki/存储库](https://docs.google.com/presentation/d/1-MkBWneCQc6pRMOEtKWMqMjJj0w-TcRkkJtLe00WaoY/edit?usp=sharing)
*   [开发人员和审计人员指南](https://docs.google.com/presentation/d/1S-H3Fntdfen06774zriXqRx-c7o622JiH6xtuuI1X7s/edit?usp=sharing)
*   [评估章](https://docs.google.com/presentation/d/1gk3rSEOUBDQFMEpoEZ7yx5yHclIhCuS-ELRHjrBTMiU/edit?usp=sharing)
*   [打造更好的工具](https://docs.google.com/presentation/d/17-K-UNE2tKJ7T7PLPvJNpiGOku8U6X74QjJ90fSE0XE/edit?usp=sharing)
*   [智能合同可升级性](https://docs.google.com/presentation/d/1znqufSH4A7AYQaylFKcN-AS1ot4YxpMfKUoEIs7Hklk/edit?usp=sharing)
*   [开源代码评估](https://docs.google.com/presentation/d/1e0jAlFQ4ej6oc3FfW8EkZXlxnZOxGdaXMwpCtjEIJUM/edit?usp=sharing)
*   [不信任问题(又名。智能合同访问控制问题)](https://docs.google.com/presentation/d/1GdgLoVQRkjti26LlK0rBc87cyUkLS0tVXUQvECgFUfY/edit?usp=sharing)

每个工作组都定义了问题，概述了潜在的解决方案，创建了行动计划，并选择了拥护者来领导工作组向前迈进。

我们对活动的成功、建立的联系和知识共享感到欣喜若狂，并正在筹备一个工作组聚会，作为 DevCon 之前在布拉格举行的 Status Hackathon 的一部分。

## 安全调查结果

我们将分两部分展示调查结果。第一种是通过为社区访谈选择的突出生态系统的成功和需求的顶级报价。第二种是通过访谈过程和无会议中发现的趋势。主要文档的链接如下。

*   [社区访谈记录](https://docs.google.com/document/d/1ATN1jKfG0gloxyEE8qyUMn4l5orkHp3QHK1-6C-rzyY/edit?usp=sharing)
*   [综合调查结果](https://docs.google.com/document/d/1z3cNRRRcVOikTqLJZAauwLEaWMwp32M81IaD7Khmvf4/edit?usp=sharing)
*   [会议工作组](https://drive.google.com/open?id=1mdbRhB6bCuEIc26zJMJMB9glbKdErePK)

以下是从社区采访中挑选出来的一些热门语录，最初由 Robbie Bent 在推特上发布。

1.  安全审计并不是说代码是安全的。审核的结果是您了解了您编写的代码，并且您的代码做了您想要它做的事情。 [**@** dguido](https://twitter.com/dguido)
2.  如果你不建立升级机制，你就完了。可升级性正在成为行业标准，并最大限度地降低了系统出现缺陷的风险。[**@**izqui 9](https://twitter.com/izqui9)/[/**@**soh kai](https://twitter.com/sohkai)
3.  我们需要一个通用的智能合约漏洞数据库来跟踪和披露问题[https://cve.mitre.org/](https://cve.mitre.org/)——它可以分散、自主地在区块链上完成。 [**@** __Tux](https://twitter.com/__Tux)
4.  Remix 是对审计人员最有帮助的工具。很少有东西可以让你查看整个代码库，可以编译、测试和调试它。[**@**cryptodavid w](https://twitter.com/cryptodavidw)
5.  工具会引导您找到需要更多关注的地方，但永远不会取代人工审查。 [**@** 毛里连](https://twitter.com/maurelian_)
6.  包管理器将是 crypto 的一个重要里程碑。代码重用是通过“复制粘贴”完成的——某个版本在某个时间点被复制，而所有的社区安全更新都被遗漏。 [**@** maraoz](https://twitter.com/maraoz)
7.  有一种倾向是雇佣攻击性的安全措施，如笔测试和代码审计。让某人在设计阶段专注于编写代码的安全性。 [**@** 找 _ 恶](https://twitter.com/find_evil)
8.  对提供可靠功能子集的语言感到兴奋，这些功能可以防止某些类型的错误。智能合约编程类似于 C，但应该更接近 Rust。 [**@** PolySwarm](https://twitter.com/PolySwarm)
9.  我们需要让开发人员在编写代码时更容易考虑安全性——为了更好地理解安全性，需要威胁建模和 OWASP 等漏洞的具体示例。 [**@** mhswende](https://twitter.com/mhswende)
10.  我们创建了一个 bug 预测市场，为审计后发现 bug 提供激励。审计师可以在一段时间内用自己的声誉来承担责任。 [@SolidifiedHQ](https://twitter.com/SolidifiedHQ)
11.  我们正在构建一项服务，在这项服务中，您可以看到所有的智能合同和令牌，并且已经过安全专家的审核。您可以检查哪些合同被审计，以及发现的错误是否被实际修复。 [**@** 雅各布平斯基](https://twitter.com/jakublipinski)
12.  我们拥有的衡量某项实践安全性的唯一真实指标，是一份合同持有的金额以及它在 mainnet 上存在的时间。 [**@** 尸体](https://twitter.com/Corpetty)
13.  审计需要技巧，目前还不能自动化——从手动测试溢出、安全数学、发送问题、验证循环开始，并跟踪整个实施过程。 [**@** 克莉赛](https://twitter.com/clesaege)

以下是来自社区访谈和 unconf 工作组的观点和趋势

*星号表示已经有工作组在开展工作的主题

**以太坊安全组织***

我们需要一个中立的行业机构来组织和促进有关安全相关问题和社区项目的明确信号。该机构的主要工作将是设立工作组和管理其产出，安排和组织会议，以及管理和协调沟通渠道和知识库。组织还应促进对空间和房屋社区资产(如潜在开发人员指南或评估印章)中所需项目的奖励/授权。让 ETHSecurity 作为这个机构开始是有意义的，并且随着项目的发展，由机构群体来确定其结构也是有意义的。

**安全 Wiki 和集中式资源库***

该领域最大的抱怨之一是难以找到相关资源。许多主题已经有关于它们的文章，但是它们不容易找到或访问。这损害了发展和教育。如果有一个地方，任何进入这个空间或贡献内容的人都可以去那里寻找或发布知识，那就太好了。

**开发者和评估指南***

一个非常流行的想法是为智能合同开发生命周期制定指导方针，这将鼓励安全实践并简化评估过程。与此同时，有一个评估过程的指导方针的愿望，以便当开发者雇佣公司时，他们可以合理地确定过程将是什么以及他们支付什么。

**开发人员培训和教育**

培训和教育是许多受访者的一个症结。这是以太坊的一个缩放问题。随着越来越多的开发人员进入这个领域，他们需要获得安全开发的工具和资源。这可以放在安全维基中，所需的内容可以由行业安全机构付费或制作。产生的任何指导方针都可以作为新开发人员工作的一个很好的框架。

**工具***

这个话题浩如烟海。从社区成员公司已经创建的工具，到帮助可视化智能合同系统的访问控制和所有权的工具，到帮助确保 GDPR 合规性的工具，再到编写审计报告脚本并帮助估计评估一组智能合同所需的工作量和复杂性的工具。如果能看到更多关于形式验证、模糊测试器和符号执行的研究和项目，那就太好了。确实出现的一个观点是，工具只能帮助发现问题，它们永远不会完全取代正式审计的工作。

**评估章***

在采访中，评估印章被证明是一个有争议的想法。这个想法是向投资者和消费者提供一个信号，表明一个应用程序在开发过程中所经历的严格程度。该标记将保存关于执行了什么安全评估、何时执行、由谁执行以及在什么代码上执行的数据。只有接受评估的人执行了审计公司的建议，才能获得资助。该历史还允许对原始评估进行元审计。不利的一面是，项目将寻求这种印记，只是作为一种营销工具，而不是提供其他人认为他们可能提供的价值。

**开源审计和审计基金***

有许多可以审计的通用工具。EVM、geth、坚固性优化器等等，都可以被仔细检查。geth 和其他一些最近的工作已经完成，但是作为一个生态系统，我们需要关注许多开发者依赖的工具。同样，我们需要考虑只有一个或几个维护者，并且不属于大公司的开源项目得到这些审计的过程。也许我们应该创建一个基金或促进这些努力的赠款。

**保险作为一种保护机制**

另一个有争议的话题。为了获得主流的采用，用户希望有某种保证，他们不会失去他们投入到系统中的所有价值。保险可能是减轻这种风险的好方法。另一方面，保险可能只是一个权宜之计，允许我们在开发实践中保持较低的标准。

**密钥管理**

密钥管理一次又一次成为最大的攻击媒介。在浏览器中对按键的不当操作、在家中丢失种子短语以及受到橡胶软管加密(身体伤害)的威胁都是让人们夜不能寐的事情。这个问题似乎没有很多答案，但它是一个大问题。

**数据隐私**

这似乎是一个更传统的科技话题，但它同样适用于区块链公司，GDPR 仍然是一个非常令人担忧的问题。这个问题也没有任何答案，但它也让人们夜不能寐。

**常见漏洞数据库**

这个想法是为智能契约错误创建一个类似 MITRE 的跟踪系统。非常简单。可以实现。可以生活在安全维基上。

**Vyper**

Vyper 是一种智能契约语言，旨在减少工作量，以确保生态系统更加安全。它作为生态系统中的一种必需资源被多次提及。我们应该鼓励它的发展和采用。

**NPM(节点包管理器)安全**

由于区块链的数据层难以置信地难以攻击，现在用户的密钥成为了目标，而传统应用程序的其余部分使其变得脆弱。如果有一份正式审计的国家预防机制包清单，让社区可以依赖，那就太棒了。

**Bug 预测市场**

人们多次说过，审计师和客户之间的激励不一致，主要是因为审计师的角色在代码评估后就结束了。Bug 预测市场有助于解决这种错位。

**白帽黑客联盟**

一个黑客联盟，跟踪个人开发者的教育和培训，并在经验丰富的老手中建立声誉。可能包括一个像 EtherNaught 或 CaptureTheEther 这样的程序和一个带排行榜的 bug bounty 平台。

**工具的通用基准套件**

我们如何衡量我们构建的工具的有效性？如果我们依靠这些工具来指出漏洞，我们当然希望提高它们的准确性。社区中已经有了一些这方面的努力，这是值得支持的。

**身份**

身份本身就是一个巨大的兔子洞，也是一个非常大的问题。目前还没有很好的解决方案，但是身份作为一个与安全相关的问题被多次提出。我们应该考虑向这个方向提供资金。

**访问控制最佳实践***

“onlyOwner”是智能合约上实现的唯一常见访问控制。这也是许多合同中最薄弱的一点，因为这是一个单点故障。如果你丢失了这个密钥，你就失去了系统的安全性。同样，这又回到了密钥管理，但是看到智能合约中复杂的访问控制出现一个标准会很酷。

## 后续步骤

如果你对这些话题中的任何一个感兴趣，请深入研究[柏林研讨会的综合结果](https://docs.google.com/document/d/1z3cNRRRcVOikTqLJZAauwLEaWMwp32M81IaD7Khmvf4/edit?usp=sharing)、[原始采访笔记](https://docs.google.com/document/d/1ATN1jKfG0gloxyEE8qyUMn4l5orkHp3QHK1-6C-rzyY/edit?usp=sharing)和[成果](https://drive.google.com/open?id=1mdbRhB6bCuEIc26zJMJMB9glbKdErePK)。有更多更详细的想法在那里表达。

加入我们的工作组吧！我们将很快发布工作组的日期和时间，并欢迎人们加入我们。大多数小组工作是通过电报进行的。给我打电话@ captnseagraves，我可以把你接到正确的组。

让我们继续在[以太坊魔术师论坛](https://ethereum-magicians.org/t/ethsecurity-report-responses/1347)上的对话吧！如果您有任何资源要添加到 wiki，请在我们的 Github 上提交 PR [。](https://github.com/ethsecurity/berlin-workshop)

我们的下一次 ETHSecurity 社区电话会议将于 10 月 5 日星期五上午 11 点举行。我们希望您能参加！

我还要感谢通过采访、电报、社区电话、非会议和工作组贡献时间的每一个人。到目前为止，这是一个爆炸，我很想知道它会走向何方。我会试着给尽可能多的人做标记。[罗布·本特](https://medium.com/u/5f4741b0ff52?source=post_page-----57431ae0ed5e--------------------------------)、[布莱恩特·艾森巴赫](https://medium.com/u/25a9ebfda673?source=post_page-----57431ae0ed5e--------------------------------)、[雷克斯·希盖特](https://medium.com/u/5d467eae2971?source=post_page-----57431ae0ed5e--------------------------------)、[罗曼·帕夫洛夫斯基](https://medium.com/u/efc4663c0c94?source=post_page-----57431ae0ed5e--------------------------------)、 [C .布朗](https://medium.com/u/9504cccc65eb?source=post_page-----57431ae0ed5e--------------------------------)、[克莱门特·莱萨格](https://medium.com/u/668e3d58152b?source=post_page-----57431ae0ed5e--------------------------------)、[卢卡斯·克雷默](https://medium.com/u/db94697c003a?source=post_page-----57431ae0ed5e--------------------------------)、杰拉德[豪尔赫·伊兹奎尔多](https://medium.com/u/515e408f0deb?source=post_page-----57431ae0ed5e--------------------------------)、[布雷特·孙](https://medium.com/u/7660e14a9789?source=post_page-----57431ae0ed5e--------------------------------)、权佑、[丹·圭多](https://medium.com/u/d6f24a8c1f70?source=post_page-----57431ae0ed5e--------------------------------)、阿德里·马萨 [利兹·斯坦宁格](https://medium.com/u/1642840a1659?source=post_page-----57431ae0ed5e--------------------------------)、[曼努埃尔·阿劳斯](https://medium.com/u/71b7575196fd?source=post_page-----57431ae0ed5e--------------------------------)、[杰姬·斯托克斯](https://medium.com/u/72682015fdc8?source=post_page-----57431ae0ed5e--------------------------------)、阿维斯塔·霍贾提[大卫·戈德堡](https://medium.com/u/654dac6a03fd?source=post_page-----57431ae0ed5e--------------------------------)、迈迪·泽罗亚利、保罗·豪纳、阿德里安·曼宁[艾萨克·帕特卡](https://medium.com/u/136e4b85be41?source=post_page-----57431ae0ed5e--------------------------------)、[布兰登·阿瓦纳吉](https://medium.com/u/771b82fd92bd?source=post_page-----57431ae0ed5e--------------------------------)、亚当·科拉尔[约翰·马尔德林](https://medium.com/u/33059aabb7e3?source=post_page-----57431ae0ed5e--------------------------------)、迪克·奥尔森[科里·佩蒂](https://medium.com/u/d7f4d00de048?source=post_page-----57431ae0ed5e--------------------------------)、爱德华·汤姆森、

感谢阅读！我们在开发者大会上见！