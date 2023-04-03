# 把 Hyperledger 的五个框架当做交通工具来购物

> 原文：<https://medium.com/coinmonks/shopping-hyperledgers-five-frameworks-as-if-they-were-vehicles-2c0d216bf681?source=collection_archive---------10----------------------->

![](img/a0f56e27686c01c5232015ea318a4630.png)

Source: [https://www.thehindubusinessline.com/companies/auto-expo-2018-renault-showcases-2-electric-vehicles/article22675633.ece](https://www.thehindubusinessline.com/companies/auto-expo-2018-renault-showcases-2-electric-vehicles/article22675633.ece)

汽车制造商每年都会在一系列的汽车博览会上推出他们的新车型。有一种新的轿车，一种新的 SUV，一种新的卡车，一种新的跑车，然后还有概念车，显然可以一分为二，但可能永远不会上市。

同样，Hyperledger 目前正在推出新的私有区块链框架。有基础模型、身份模型、移动模型、可扩展模型和智能合约模型。就像博览会上的汽车一样，并不是所有的框架都准备好上路了:目前，只有一个框架——Fabric——是活动的，而其他框架仍处于孵化阶段。

在这篇文章中，我们将回顾不同的框架，确定它们的重点，并具体说明它们为什么重要。

**Fabric**
截至本文撰写之时，Fabric 是唯一一个活动的 Hyperledger 框架。这是因为，正如 [Linux 基金会自己指出的](https://www.hyperledger.org/projects/fabric)，它“旨在作为开发具有模块化架构的应用程序或解决方案的基础。”换句话说，布料是 Hyperledger 的旗舰，摸遍了所有的基地，得到了区块链的私人许可。

以下是 Hyperledger 用来解释面料的视频:

人们可能会认为，Fabric 有一个分类账，使用智能合约，并包括一个参与者管理其交易的系统。

因为 Fabric 是私有的和许可的，与比特币区块链不同，Fabric 需要一种方式来允许其参与者注册其服务。Fabric 通过提供成员服务提供商(MSP)来实现这一点。

虽然面料是 Hyperledger 的标准区块链产品，但这并不是说它是一刀切的。事实上，Fabric 是模块化的，允许用户选择他们的一致算法、MSP 以及他们希望存储分类帐数据的格式。

考虑到 Hyperledger 结构是私有的，并且有时可能包括经常是竞争对手的参与者，它的[还提供了创建渠道的能力，允许一组参与者创建单独的交易分类帐。](https://hyperledger-fabric.readthedocs.io/en/release/blockchain.html)“什么是使用渠道的好例子？假设有人想向某些参与者提供特殊费率或服务，但不想向其他人提供。一个渠道允许他们谨慎地这样做。

简而言之，把 Hyperledger 面料想象成区块链框架中的本田雅阁或丰田凯美瑞:它并不性感，但它会带你去那里，而且很管用。

## *印第*

Hyperledger Indy 的重点是分散身份。正如 [Hyperledger 指出的](https://www.hyperledger.org/projects)，它“为创建和使用基于区块链或其他分布式分类账的独立数字身份提供工具、库和可重用组件，以实现互操作性。”
印第为什么重要？它有几个关键功能。首先，我们中的许多人依赖政府来确认和签发我们的驾照和护照，它们是我们主要的身份证明。但是，如果一个政府拒绝这样做或者对其公民使用身份识别的权力呢？Indy 通过允许一个公正、分散的政党来解决这个问题。其次，对于互联网用户来说，Indy 解决了登录时用户名和密码的长期问题，不仅仅是在一个或另一个网站，而是在整个互联网上。
第三，对于公司来说，这意味着他们可以将客户服务员工从解决登录问题中解放出来，根据 [Sovrin](https://sovrin.org/) 的说法，这些问题目前占联系呼叫中心工作量的 30-40%。这也意味着公司将不必保留客户的敏感信息——作为更容易被黑客攻击或成为黑客攻击目标的集中位置——因为客户将控制他们的身份以及谁可以访问他们。

继续汽车的比喻，Hyperledger Indy 可能是区块链框架公司的法拉利 488 GTB:它很酷，它可能会帮助你摆脱当局，当你开车过来时，人们会知道是你。

## 锯齿湖

由英特尔提供的 Hyperledger Sawtooth 专注于构建、部署和运行分布式分类账，这种分类账特别具有伸缩性。这种对分布式分类帐技术的关注使锯齿成为供应链和物流行业的最爱，正如 Hyperledger 的这段视频通过其钓鱼示例所示:

在车辆方面，Hyperledger 锯齿是区块链框架的 UPS 或联邦快递卡车。

## 挖掘

Hyperledger Burrow 框架的重点是智能合同。[根据 Hyperledger](https://www.hyperledger.org/projects/hyperledger-burrow) 的说法，它甚至提供了“一个模块化的区块链客户端，带有一个许可的智能合同解释器，部分是根据[以太坊虚拟机](https://themerkle.com/what-is-the-ethereum-virtual-machine/)的规范开发的。”

现在让我们更进一步。Burrow 可以在各种不同的地方创建和执行智能合同。据帮助创建 Burrow 的 Monax 公司称，Burrow 可以从“内部企业部署，到联合企业部署，再到更分散的公共部署。从需要使用价值令牌的部署，到只需要使用区块链但不需要货币化令牌的部署。”这是一大堆行话，但据我所知，陋居也会通过使用代币来处理支付的智能合同。就陋居是哪种交通工具而言，想想布林克的卡车吧。

## 伊洛哈

与其他 Hyperledger 项目不同，Iroha 是用 C++编写的，c++是一种比 Go 语言更受欢迎的编程语言，背后有更多的开发人员。Iroha 运行在 C++上，因为它的重点是移动应用程序开发。它与其他框架的不同之处还在于，它使用 [Sumeragi](https://github.com/hyperledger/iroha/wiki/Sumeragi) 作为其基于链的拜占庭容错一致性算法。

为了完成运行汽车的类比，Hyperledger Iroha 可能是区块链框架的 DeLorean。这听起来可能很酷，但是你最近有没有看到很多德罗宁人开车到处跑？我确实希望 Iroha 一切顺利，但是当我读到它的时候，我经常会头晕，因为它很快就从移动业务中脱离出来，承诺比 1.0 版本可能提供的更多。

## 关注更多

更多关于商业区块链 Hyperledger edX 课程介绍、《Hyperledger 区块链》和《区块链》的帖子，请关注我以获取最新消息。