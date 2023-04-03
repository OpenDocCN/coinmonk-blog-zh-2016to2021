# 轻量级合同—后续措施

> 原文：<https://medium.com/coinmonks/lightweight-contracts-next-steps-9a88a60e5fec?source=collection_archive---------5----------------------->

## 因为智能合约并不那么智能

![](img/9fe5d32f9d3059be6c8ecc20d2dccb39.png)

Lightweight Contracts — Next Steps. Photo by Iker Urteaga on Unsplash

正如承诺的那样，8 月 17 日，Ardor 轻量级合同解决方案作为版本 2.1.0e 的一部分在 Ardor testnet 上上线，该版本可从[jelurida.com](https://www.jelurida.com/)获得。

任何人现在都可以原型化和试验 Ardor contracts 框架的独特设计和功能，我在以前的[文章](/@lyaffe/lightweight-contracts-articles-49c3032a50da)和这个[视频](https://youtu.be/PVP3Qy46tHQ)演示中对此进行了深入解释。

下一步，我们希望完成一些技术债务，并通过进一步完善所需的工具，使技术尽可能地为开发人员所用。

在我们计划于 2018 年 9 月发布的下一个版本 2.1.1e 中，我们将重点关注三个主要开发领域:

1.  合同管理器插件—简化合同部署和维护。
2.  合同验证框架—验证部署到区块链的合同确实基于特定的源代码。
3.  API 调用者——契约功能的独立构建块。

## 合同管理器插件

正如我们在 [wiki](https://nxtwiki.org/wiki/Lightweight_Contracts) 中解释的，合同部署是一个两步过程，最好使用合同管理器命令行实用程序自动完成。如您所知，我们提供了开箱即用的 IntelliJ 项目，帮助您快速开始合同开发和测试。合同管理器插件将合同管理器工具打包成 IntelliJ 插件，作为 Ardor 安装的一部分。您所需要做的就是将它安装到您的 IntelliJ IDE 中，然后就可以开始工作了:您可以从项目内部部署和管理您的合同。

## 合同验证框架

与所有新技术一样，了解任何固有的风险并降低风险非常重要。我们预测，针对合同的一种流行的攻击手段将是在不提供源代码的情况下将恶意合同部署为类文件，然后通过社会工程方法欺骗用户使用它，如冒充合法的合同开发者。为了减轻这一点，我们强烈鼓励合同开发者发布他们的源代码。“合同验证框架”将允许给定合同源代码的任何人验证该源代码确实是作为类文件部署到区块链的代码。

## API 调用方

在开发合同时，Ardor 提供的主要工具之一是一个使用 API 调用程序对象的现有 Ardor APIs 的简单接口。这些调用者对象本身是有用的，甚至在契约上下文之外也是有用的，例如对于客户端开发人员。在下一个版本中，我们将使 API 调用程序独立可用，并提供如何将它们集成到您的客户端应用程序中的有用示例。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)