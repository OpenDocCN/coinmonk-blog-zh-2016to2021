# 如何将您的 Hyperledger Fabric PoC 应用程序部署到云

> 原文：<https://medium.com/coinmonks/how-to-deploy-your-hyperledger-fabric-poc-application-to-cloud-a293112ab72f?source=collection_archive---------0----------------------->

![](img/b56d5ab00631e4b8d2671a3ccf2c21cf.png)

[image source](https://www.google.co.in/url?sa=i&source=images&cd=&cad=rja&uact=8&ved=2ahUKEwioi62T6YLgAhXHXSsKHUd5D7sQjxx6BAgBEAI&url=https%3A%2F%2Fwww.hyperledger.org%2Fblog%2F2018%2F10%2F26%2Fhyperledger-fabric-now-supports-ethereum&psig=AOvVaw1dxZL59R01aNyekrIsHaKh&ust=1548295715938898)

因此，您已经学习了如何构建 Hyperledger Fabric 区块链网络，还学习了如何用 Node.js 或 Fabric 支持的其他语言创建链码，以及如何在这样的网络上开发 web 应用程序。

然而，有一个限制，它们在你的 Ubuntu 操作系统或虚拟机中。利益相关者和其他意向方将无法尝试或使用它。在这种情况下，他们除了证明你有这样的技能之外，不会产生任何价值。因此，我们需要找到或购买一些服务器，并在这些服务器上部署网络，以形成区块链网络并运行其应用程序，或者从云服务提供商那里租赁一些服务器，并将我们的结构区块链部署到这些服务器上。第二种选择更经济有效。所以，让我们选择这个选项。

要做到这一点，我们应该采取以下步骤:

流程 A —确定符合您需求的云服务提供商。亚马逊 AWS 是一个选项，数字海洋(DO)是另一个选项。然后注册一个。在这篇文章中，我将写关于 DO 的内容。

流程 B —设置节点/服务器

为您的 droplet(服务器)选择操作系统及其配置。

我会推荐至少 4GB 内存的 Ubuntu 16.04 LTS，尽管 8GB 内存会更理想。

并且有三个微滴(服务器),因此我们至少有 3 个最小数量的节点。

流程 C —创建用户帐户

登录数字海洋后，使用“控制台”选项以 root 身份登录并创建一些用户帐户。

如何访问您的 droplet？

如果你使用的是 Windows 操作系统，我有一个技巧可以让你的生活更轻松。

流程 D-下载并安装 Hyperledger Fabric，然后测试一些示例应用程序

流程 E —将您的链码和相关文件复制到 Fabric 安装下的适当文件夹中(技术)

流程 F —为支持服务器端脚本语言的 web 服务器提取 docker 映像

过程 G —为您的 web 服务器创建和设置目录结构

过程 H——将服务器端源代码复制到 web 服务器下的适当目录中(技术)

流程 I —启动 REST API 服务器(重要的流程/服务)

流程 J——为您的 web 服务器创建 docker 容器

流程 K —启动/安装您的 web 服务器 docker 容器(重要的流程/服务)

答对了。现在，集成了运行在 Hyerledger Fabric 上的 chaincode 的 web 应用程序已经准备好了！

重复这些过程为您的水滴 2 和 3 或更多…

一旦完成，你可以使用 docker swarm 将这些水滴变成区块链网络(我的第一套 Hyperledger Fabric 培训材料涵盖了这一点)。

希望以上为您提供了一个清晰的 Hyperledger Fabric 云部署路线图。

与此同时，你只需花很少的费用，就可以获得其中一些重要过程的四种技术的细节，这样你就可以更快更轻松地完成它们。

顺便说一下，我假设你已经阅读了我题为“使用 Hyperledger Fabric 构建区块链 PoC 应用程序”的文章([https://medium . com/coin monks/Build-a-区块链-PoC-Application-using-Hyperledger-Fabric-6b be 633 c 2204](/coinmonks/build-a-blockchain-poc-application-using-hyperledger-fabric-6bbe633c2204))。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)