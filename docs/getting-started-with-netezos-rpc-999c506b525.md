# Netezos 入门。位置遥控(remote position control)

> 原文：<https://medium.com/coinmonks/getting-started-with-netezos-rpc-999c506b525?source=collection_archive---------1----------------------->

![](img/a5b31c37cb5c9dcb328d6b9647dcdd09.png)

Netezos 是。NET 标准 2.0 库包，用于使用 Tezos。现阶段，Netezos。Rpc 已实现。它通过 RPC API 提供对 Tezos 节点的访问。让我们深入研究一下，看看它是如何工作的。

# 装置

Netezos。Rpc 包在 [NuGet](https://www.nuget.org/packages/Netezos.Rpc/) 上可用，这是一个用于。NET 开发人员。所以可以安装 Netezos。通过 Nuget 包管理器 GUI 或通过以下命令进行 Rpc:

`PM> Install-Package Netezos.Rpc`

或者只是从 [GitHub](https://github.com/baking-bad/netezos) 资源库中克隆项目。

# 基本用法

有一个主类`TezosRpc`，您需要它来构建查询，由 Tezos RPC API 支持。

让我们创建一个`TezosRpc`类的实例，构建一个简单的 GET 查询并通过调用`GetAsync()`来执行它。

请注意，真正的 HTTP 请求只有在您调用`GetAsync()`时才会发送。在此之前，您只能使用 query 对象，该对象也可用于获取子查询。

# 访问块

有两种方法可以访问任何块:前向索引和后向索引。

# RpcList 和 RpcDictionary

许多 RPC API 方法的结果可以解释为数组或字典，它们允许您通过指定键或索引来获得许多对象或仅获得一个对象。

# 查询参数

如果某个 RPC API 方法有查询参数，相应的查询对象将有被覆盖的`GetAsync()`方法。

# 发布方法

使用 POST 方法，您可以传递 JSON 字符串输入。

第二个选项—将对象传递给 PostAsync 方法。

第三个选项—将特定的参数传递给 PostAsync 方法。

# 结论

在本文中，我们描述了最常见的用例。Netezos。RPC 是非常灵活的，所以实际上可以通过 RPC API 用于更多与 Tezos 区块链相关的情况。

Netezos 是一个开放开发项目，所以请不要犹豫，通过创建问题或向我们的 GitHub repo 提出请求来参与。此外，我们总是很高兴在我们的[烘焙坏电报聊天](http://t.me/baking_bad_chat)中讨论任何功能。

干杯！

*最初发表于 2019 年 7 月 10 日*[*https://baking-bad.org*](https://baking-bad.org/blog/2019/07/10/getting-started-with-tezos-library-netezos-rpc/)*，在那里你可以找到文章的完整版本。*

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)