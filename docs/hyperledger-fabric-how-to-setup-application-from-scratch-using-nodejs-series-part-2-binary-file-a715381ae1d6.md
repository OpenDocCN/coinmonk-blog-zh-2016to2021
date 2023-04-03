# Hyperledger Fabric:如何使用 Nodejs 系列从头开始设置应用程序—第 2 部分

> 原文：<https://medium.com/coinmonks/hyperledger-fabric-how-to-setup-application-from-scratch-using-nodejs-series-part-2-binary-file-a715381ae1d6?source=collection_archive---------6----------------------->

## 二进制文件介绍和解释..

![](img/822d6a8e29b6f949c04b039edef666e8.png)

Source : [Google](https://www.hyperledger.org/blog/2018/03/20/hyperledger-fabric-v1-1-released)

在我的[上一篇文章](/coinmonks/hyperledger-fabric-how-to-setup-application-from-scratch-using-nodejs-series-1-to-7fb5c56877c7)中，我们从介绍&如何设置 Hyperledger Fabric server 开始。我们还讨论了实时用例&的快速故事，区块链如何支持该行业。本文对下面的二进制文件&以及我之前提到的 4 个组件(ORDERER、CA(CERTIFICATE AUTHORITY)、PEERS & COUCHDB)没有深入讨论。

我们开始吧..

因此，当您从 fabric-samples 设置“构建您的第一个网络(byfn)”时，您会在 **bin 文件夹**中看到这些二进制文件

*   `cryptogen`，
*   `configtxgen`，
*   `configtxlator`，
*   `peer`
*   `orderer`和
*   `fabric-ca-client`

在 fabric server 中，每个二进制文件做的事情都是特殊的。当你开发自己的网络时，你必须声明或定义谁要做什么？。例如，让我们举一个实时的例子。这是一个典型的超市故事，售货员会帮助顾客找到商品，管家会确保环境干净，账单部门的人只做账单的工作。同样，在部署网络时，您必须定义谁将做什么？。(阅读 Hyperledger 官方文档中的交易流，以便更好地理解:[单击此处](http://hyperledger-fabric.readthedocs.io/en/latest/txflow.html)了解调用交易时发生的情况)..因此，在一个网络中，我们有 4 个组件(订购方、对等方、CouchDB、CA ),将决定由谁来执行这些角色，并将生成各自的身份/访问。

例如，当新员工加入组织(公司)时，我们向他/她提供门禁卡/身份证，对吗？类似地，在结构网络中，我们必须定义身份，这将由 MSP(成员资格服务提供商)提供。为了完成这项工作，我们使用二进制文件 **cryptogen。**

现在，我们来谈谈 **configtxgen..**又是一个超级市场的实时例子，在一天开始的时候，他们创建一个虚拟的计费 ID，以确保计费应用程序运行良好&也是为了商誉。这个虚拟计费 ID 实际上没有增加任何价值，但是他们以虚拟计费 ID 开始一天的工作，并以此开始当天的计费编号。同样，在区块链，我们有一个称为**“Genesis”模块(初始化区块链网络或通道的配置模块，也是链上的第一个模块)**。**。我不会说这是虚拟的，而是链条中非常关键的一环，它实际上是区块链的“起点”。 **Configtxgen** 实际上做了这项工作，它为网络创建了 genesis 块。**

现在到了`**configtxlator**`(阅读此文[点击此处](https://github.com/hyperledger/fabric/blob/master/examples/configtxupdate/README.md) ) 的时候了，我可以将此与“优化器或升级器”联系起来。例如，您有带通道的网络设置，当您想要修改通道或更新 genesis 块时，您需要编辑相同的内容，configtxlator 文件帮助您首先将块转换为人类可读的形式&一旦您做出必要的更改，它会提交&升级相同的内容。

`**configtxlator:**`标准用法预计为:

1.  SDK 检索最新配置
2.  `configtxlator`生成人类可读的配置版本
3.  用户或应用程序编辑配置
4.  `configtxlator`用于计算配置更新表示对配置的更改
5.  SDK 提交签名并提交配置

**对等(直进)**，有助于执行一组动作。例如，使用**对等通道**子命令，您可以将对等体添加到特定通道，**对等链码**帮助您将链码部署到对等体&等等。

**fabric-ca-client** 命令允许您管理身份(包括属性管理)和证书(包括更新和撤销)。

使用这个二进制文件，我们可以启动订购程序。它将使用生成的“创世纪块”。如果不可用，它将使用 SOLO orderer 配置文件生成新的 genesis 块。

涵盖了所有这六个二进制文件之后，理解我们在什么情况下使用它们是非常重要的。让我们从全新的网络开始，绝对全新的网络&我们将使用上述二进制文件进行设置..并定义这四个组成部分。

让我们在我的下一篇文章中见面吧..如有任何问题/意见，欢迎发帖讨论。

如果你喜欢看这个系列，请不要忘记鼓掌&与他人分享。关于以上二进制文件如何工作的任何问题/评论或任何不同的想法，请随时在评论区分享..

很快在下一篇文章中再见..