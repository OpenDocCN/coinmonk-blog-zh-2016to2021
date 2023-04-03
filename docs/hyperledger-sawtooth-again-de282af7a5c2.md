# Hyperledger 锯齿——框架的初步概述

> 原文：<https://medium.com/coinmonks/hyperledger-sawtooth-again-de282af7a5c2?source=collection_archive---------10----------------------->

![](img/7af409d4973dd44107897e50f0560cbf.png)

以下是我从英特尔对锯齿的开发介绍中学到的东西。

## 先决条件

不久前，我在我的 windows 机器上安装了一个旧版本的 [docker，试图摆弄 Hyperledger fabric。我放弃了使用它，而不是在谷歌上搜索如何解决我遇到的问题，这个问题与 BIOS 设置中没有启用硬件虚拟化有关。](https://docs.docker.com/docker-for-windows/install/)

更新后，docker 运行流畅，并正确响应 docker-version 命令。

有了 docker 的全新安装，您基本上可以开始在锯齿设置中执行一些命令，您可以从[的合成文件](https://sawtooth.hyperledger.org/docs/core/releases/1.0/app_developers_guide/sawtooth-default.yaml)中获得，这实际上是一个设置的文本文件，使 docker 能够为您配置一个环境，以部署类似独特虚拟机的容器。在这种情况下，有四种可能的容器:

1.  用于 cURL/http 调用的 REST api，连接到验证器
2.  验证器使用开发模式来验证事务和块
3.  客户端发布事务并查询 API 的节点、块、事务
4.  用于设置(建议)和 intkey/XO(演示)事务的处理器

> Docker 合成文件指定了要从 Docker Hub 下载的容器映像以及所有容器正确通信所需的网络设置。

对我来说超级直截了当，希望你也是。

## 微不足道的

由于内存不足，我浏览了一下建议我打开的终端，然后针对这种环境使用了一些命令。

首先，我必须用“docker-compose”和“up”命令设置默认环境。在完成了对每个容器的拉取和暂存之后，我就可以启动可以连接到客户机容器或验证器容器的终端了。我在直接连接 REST API 时遇到了一点问题，但谢天谢地，该功能仍然可以通过其他容器操作。

留给我的是一个初始化的区块链，除了一些网络设置、起源区块和(我认为)验证器地址之外，它没有包含太多其他内容。

在一切都设置好之后，我被指示对客户机容器进行实验。我发出的第一个命令是 REST API，允许我收集分类帐中的块列表。在分类帐中记录了所有块的列表后，我还能够通过特定的 ID 检索块。

我后来试验的函数对于这个容器来说相对简单。我必须创建一批 10 个 intkey 事务，然后通过将生成的数据加载到 REST API 的接口上，将它加载到一个块中。

然后，我能够检索参与网络的所有节点的列表，包括验证器和一些生成的节点。

完成客户端容器后，我在另一个终端窗口中登录到验证器容器，并发出一些设置事务(建议)，这些设置事务用于更改可接受的指令集或事务族，以包含版本 1.0 的 intkey 和 sawtooth 类型。

## 结论

嘿，一个结论。超级难得我写:O

最后，这是对锯齿和 Dockerized 环境中的基本过程和功能的一个相当清晰的介绍，这对于为企业应用程序的未来开发打下坚实的基础是必不可少的？…

谢谢你过来！

如果你对这一切发生在哪里感兴趣，可以看看这个链接:[锯齿](https://sawtooth.hyperledger.org/docs/core/releases/1.0/app_developers_guide/docker.html)