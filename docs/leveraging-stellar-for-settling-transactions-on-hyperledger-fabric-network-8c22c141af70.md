# 利用 Stellar 在 Hyperledger Fabric 网络上结算交易—第 1 部分

> 原文：<https://medium.com/coinmonks/leveraging-stellar-for-settling-transactions-on-hyperledger-fabric-network-8c22c141af70?source=collection_archive---------1----------------------->

# 什么是 Hyperledger Fabric 和 Stellar？

**Hyperledger Fabric，**是一种私有的、经许可的区块链网络技术，它已经变得非常流行，并正在许多生产网络中使用，旨在创建一个共享的分类帐或数字资产的公共视图，以表示真实世界的实物资产以及与之相关的交易。如果想了解更多，可以从 [*官方页面*](https://hyperledger-fabric.readthedocs.io) 开始。

要设置网络并安装智能合约样本，您还可以查看 [*IBM 区块链平台的启动计划*](https://console.bluemix.net/docs/services/blockchain/starter_plan.html#overview) *。*首次用户还可以获得 500 美元的积分来探索平台。详情请点击以上链接。

**Stellar，**是一种公共的、无许可的分布式账本技术，它有助于低成本支付，利用由不同锚(发行数字资产的参与组织)发行的本地加密货币流明(XLM)或数字资产。

虽然像 MTO、银行甚至个人这样的任何组织都可以成为锚并发行资产，就像借条一样，但在相关各方信任资产发行者及其兑现借条的能力之前，这些资产不会用于交易。

虽然这些资产可以是任何东西，但在大多数情况下，它们代表锚以给定的法定货币持有的现金存款。这种资产通常被称为“稳定货币”，因为它的价值与法定货币挂钩，如美元、欧元等，与加密货币相比，通常波动很小。

Stellar 还提供了一个分布式交易所，以促进锚定资产和流明的交易。Stellar 的另一个重要特性是，虽然任何人都可以加入该网络，但金融机构可以选择与他们信任的特定组织进行交易，并可以在最终用户成为其客户之前获得该组织执行的 AML 检查的证明。

因此，它不是一种非中介技术，允许银行和金融机构在监管框架内工作，同时使支付过程对客户来说更有效。

# Hyperledger Fabric 和 Stellar 之间的集成案例:

Hyperledger Fabric 是一个私有网络，因此，如果在资产转移之前有交易需要**付款证明**，这不是一个简单的过程。下面列出了几个选项

1.  在智能合约中，为<contract>硬币或<contract>代币创建资产定义，例如，对于处理能源交易的智能合约，该资产可以称为“*能源硬币*”。让一家或多家银行成为网络的一部分，让它们充当代币发行者。银行可以帮助兑换真实货币，并在网络上向参与者发行等量的代币，反之亦然。但这种方法的问题是，令牌仅限于这个网络，每次参与者想要将令牌转换成实际现金并在其他地方使用时，银行都需要参与。</contract></contract>
2.  另一种方法是以如下方式使用类似 Stellar 的公共网络:

a)使用像 stellar 这样的公共网络，根据 hyperledger 结构网络上存储和约定的数据进行支付

b)通过调用 fabric smart contract 中的 stellar APIs 验证付款已经完成，然后按照 fabric network 中的约定完成资产转移。

通过这种方式，通过 Stellar network 支付收到的代币可以在认为合适的情况下部署使用。此外，随着联邦保险，稳定的硬币，如[据点美元，](https://stronghold.co/stronghold-usd/)这些天可用，最终用户可以在恒星上交易这些数字资产更有信心。

# 如何实现 Hyperledger Fabric 和 Stellar 之间的这种集成？

在高层次上:

a)Stellar 提供 SDK 和 REST APIs 与网络交互。

b)所有交易都需要由授权方签字，任何有权访问网络和账户 Id 的人都可以验证向账户支付的款项

c) Fabric smart contract 可用于记录与用例相关的专有和私有信息，以及各方之间就特定交易的支付金额达成的协议。

d)可离线调用 Stellar API 进行支付，并且可通过使用 Stellar API，将此支付的验证或任何所需的对账作为结构智能合同的一部分来完成。

为了更好地理解这一点，让我们考虑基于 hyperledger fabric 的弹珠智能合约 *，*，它可以在任何 fabric 网络或 IBM Blockchain starter plan 上轻松部署。请访问上面的链接，了解这份智能合同的内容。

在这里，如果我们想要建立一个工作流，根据商定的价格将弹珠从买方转移到卖方，那么这些修改将需要在智能合同和客户端应用程序中进行支付并验证支付。

1.  修改所有者结构，以包括该人的主要帐户地址。
2.  添加一个 Offer 类型的结构来保存所有购买出售的弹珠的报价，并让卖方接受或拒绝报价。
3.  一旦报价被接受并记录在 fabric 网络上，买方可以从客户端应用程序启动支付，利用 Stellar APIs 进行支付。支付 API 具有用于配置发送者、接收者、金额和备忘录的账户地址的字段。memo 字段可用于设置支付所依据的 offerId。相关代码可以在 [*这里找到*](https://github.com/sachinjha/marbles/blob/master/utils/stellar_lib.js) *。*
4.  一旦买方收到来自 stellar 的交易完成消息，他可以调用 fabric smart contract 方法来指示给定报价的交易完成。
5.  fabric smart contract 方法可以使用 stellar APIs 检查交易细节，并在按照要约完成从卖方到买方的转移之前，将发送者、接收者、金额和备忘录字段与要约中的相应值进行匹配。相关代码可在此处[](https://github.com/sachinjha/marbles/blob/master/chaincode/src/marbles/write_ledger.go)**(文件:write_ledger.go，方法:payment_complete_against_offer)。**

**示例代码显示了本地货币(流明)的使用，但是相同的 API 也可以用于任何其他锚定资产。虽然，我们还不能实现* [*原子互换*](https://hackernoon.com/atomic-swaps-simply-explained-how-to-swap-cryptocurrencies-without-a-middleman-6cd29680c32e) *，但是这种方法已经接近了，因为一旦要约已经在光纤网络中被接受并且已经在 Stellar 上完成了支付，就没有回头路了，光纤网络智能合约将确保所需的转移发生。**

*虽然可以在[这里](https://github.com/sachinjha/marbles)找到包含所有修改的最终代码(在智能合同和客户端—仅与 stellar 相关)，但是您也可以在本系列的[第 2 部分](/@sachinjha/leveraging-stellar-for-settling-transactions-on-hyperledger-fabric-network-part-2-d474a414385c)中找到关于实现这一点的步骤和 API 的详细解释。*

> *[直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)*

*[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)*