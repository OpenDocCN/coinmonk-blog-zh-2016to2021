# 使用 DeFi Pulse API 获取 DeFi 项目数据

> 原文：<https://medium.com/coinmonks/get-defi-projects-data-with-defi-pulse-api-81721f8e6dd2?source=collection_archive---------5----------------------->

## DeFi 脉冲 API

## 使用 DeFi Pulse dotnet sdk 了解最受欢迎的 DeFi 项目

![](img/e8e0d3c2227388459e248bda884bed55.png)

[https://pixabay.com/photos/leather-wallet-business-cards-visa-3080553/](https://pixabay.com/photos/leather-wallet-business-cards-visa-3080553/)

分散融资是 2020 年的一个热门话题，新项目和新想法不断涌现。2020 年的加密牛市非常令人兴奋，在此期间学到了很多东西。

DeFi(去中心化金融)是由建立在区块链上的非托管金融产品组成的，主要是在以太坊上。它们是开源的，这意味着任何人都可以对代码做出贡献，并且有一些协议，社区可以通过治理令牌投票并决定项目的未来。

# DeFi 脉冲

[DeFi Pulse](https://defipulse.com/) 是一个可以获得 DeFi 协议分析和排名的网站。社区大量使用它来跟踪协议的锁定总值(TVL)。

DeFi Pulse 提供 API 端点，我们可以从多种协议中获取数据。例如，您可以调用 API 来获得按协议锁定的总价值(TVL)的详细细分，或者从 DeFi 项目获得当前的借贷利率。API 还允许我们获得以太坊天然气价格信息，这非常有帮助。API 文档可以在下面找到:

[](https://docs.defipulse.com/api-docs-by-provider/defi-pulse-data) [## DeFi 脉冲🍇

### DeFi Pulse 提供了分散金融(DeFi)协议的最新指标和排名。它的排名跟踪总…

docs.defipulse.com](https://docs.defipulse.com/api-docs-by-provider/defi-pulse-data) 

为了调用 API，你需要一个 API 键。为了创建一个，你必须在他们的网站上注册([https://data.defipulse.com/](https://data.defipulse.com/))。发出请求时，API 键将作为查询字符串出现在 url 上。

# 构建 sdk

由于我们将对 API 进行 HTTP 调用，该类将需要通过构造注入来获取 HttpClient 类。我们将使用 IHttpClientFactory 来创建 HttpClient。这是处理 HTTP 请求时推荐的方法。

> [IHttpClientFactory 是一个由](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) `[DefaultHttpClientFactory](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)` [实现的契约，一个自以为是的工厂，自。NET Core 2.1，用于创建在应用程序中使用的 HttpClient 实例。](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)

我们将创建一个名为`DeFiPulse.cs`的类，它将接受两个参数，由 IHttpClientFactory 创建的 HttpClient 和 API 键:

DeFiPulse.cs

# 获取 DeFi 项目市场概述端点

有了 HttpClient 的实例和 API 键，我们就可以调用 API 端点。我们将下载`System.Net.Http.Json`库，并使用已经为我们做了这些的`_httpClient.GetFromJsonAsync<T>()`扩展方法，而不是做一个简单的`_httpClient.GetAsync()`，验证有效载荷，然后将其反序列化到我们的类中。为了将它添加到我们的项目中，我们只需运行以下 cli 命令:

`dotnet add package System.Net.Http.Json --version 5.0.0`

在下面的示例中，我们将调用 GetDeFiProjectsMarketOverview 端点:

Call the GetDeFiProjectsMarketOverview Endpoint

我们将得到以下 JSON 响应:

GetDeFiProjectsMarketOverview JSON response

# 将 DeFiPulse 类添加到依赖注入容器中

为了访问我们构建的 DeFiPulse 类，我们将把它添加到 DI 容器中。为此，我们将创建`IDeFiPulse`接口，并创建扩展方法`AddDeFiPulse`，如我们在下面的代码中所见。我们为`IServiceCollection`接口创建一个扩展方法，因为当使用这个 sdk 时，我们将简单地在`StartUp.cs`类中添加`services.AddDeFiPulse("Your API Key here");`。

`AddDeFiPulse extension method`

在上面的代码中，我们使用了 [HttpClient 类型的客户端，它只是一个](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#how-to-use-typed-clients-with-ihttpclientfactory) `[HttpClient](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#how-to-use-typed-clients-with-ihttpclientfactory)` [为某些特定用途而预先配置的客户端。](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#how-to-use-typed-clients-with-ihttpclientfactory)

# 创建自定义定义脉冲异常

C#有内置的异常类型，比如`ArgumentNullException`和`NullReferenceException`。我们可以创建自己的定制例外来满足我们的业务需求。在我们的例子中，我们将创建一个`DeFiPulseException`。为此，我们将创建从`Exception`类派生的类:

当调用 API 时，我们现在可以捕捉并抛出我们的`DeFiPulseException`,以获得 DeFi 协议中锁定的历史总值:

# 使用 DeFiPulse sdk

既然我们已经构建了 sdk，我们可以在自己的 API 中使用它。在下面的例子中，我们将通过构造函数注入获得`IDeFiPulse`接口，然后我们能够调用它的方法，如下面的示例控制器所示:

Use IDeFiPulse.cs

# DeFiPulse dotnet 标准库

这个完整的库是免费的，可以下载并通过运行 cli 命令添加到您的项目中:

`dotnet add package DeFiPulse --version 1.0.1`

您也可以通过 nuget 添加这个包，nu get 是的官方包管理器。NET 或通过访问 GitHub:

[](https://www.nuget.org/packages/DeFiPulse/) [## 除颤脉冲 1.0.1

### DeFi Pulse API 的 dotnet 标准包装器。

www.nuget.org](https://www.nuget.org/packages/DeFiPulse/) [](https://github.com/strykerin/DeFi-Pulse-dotnet) [## strykerin/DeFi-Pulse-dotnet

### DeFi Pulse API 的 dotnet 标准包装器。可在 Nuget 上获得。

github.com](https://github.com/strykerin/DeFi-Pulse-dotnet) 

# 结论

分散融资是一个令人兴奋的领域，它肯定会在未来几年保持增长。如果你有兴趣了解更多关于 DeFi 的知识，下面的参考资料部分有一些关于这个主题的有趣文章。

在本文中，我们为 DeFiPulse API 构建了一个 dotnet 包装器，以便从以太坊区块链获得 DeFi 协议分析、排名和天然气价格。

# 参考

[](https://www.coindesk.com/what-is-defi) [## 什么是 DeFi？-硬币台

### DeFi 是“分散金融”的缩写，是加密货币中各种金融应用的总称…

www.coindesk.com](https://www.coindesk.com/what-is-defi) [](https://blockonomi.com/what-is-decentralized-finance-defi/) [## 什么是 DeFi？了解分散式金融格局

### 在过去几个月里，随着平台和平台的激增，分散金融(DeFi)已经掀起了波澜

blockonomi.com](https://blockonomi.com/what-is-decentralized-finance-defi/) [](https://defipulse.com/) [## DeFi Pulse | DeFi 排行榜|统计、图表和指南

### 可从 token sets Name Chain Category 1 Day % DeFi 获得，它是短语“分散金融”的缩写，它…

defipulse.com](https://defipulse.com/) [](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) [## 使用 IHttpClientFactory 实现弹性 HTTP 请求

### IHttpClientFactory 是一个由 DefaultHttpClientFactory 实现的契约，它是一个固执己见的工厂，自。网络…

docs.microsoft.com](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) [](https://www.tutorialsteacher.com/csharp/custom-exception-csharp) [## 在 C#中创建自定义异常类

### C#包含内置异常类型，如 NullReferenceException、MemoryOverflowException 等。然而，你…

www.tutorialsteacher.com](https://www.tutorialsteacher.com/csharp/custom-exception-csharp) [](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/exceptions/creating-and-throwing-exceptions) [## 创建和引发异常- C#编程指南

### 异常用于指示程序运行时发生了错误。描述…的异常对象

docs.microsoft.com](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/exceptions/creating-and-throwing-exceptions) 

## 另外，阅读

*   [了解以太坊和 Web3](https://blog.coincodecap.com/go/learn)
*   [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [3 商业评论](/coinmonks/3commas-review-an-excellent-crypto-trading-bot-2020-1313a58bec92)
*   [AAX 交易所审核](/coinmonks/aax-exchange-review-2021-67c5ea09330c) |推荐代码、交易费用、利弊
*   [Deribit 审查](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和 Testnet
*   [FTX 密码交易所评论](/coinmonks/ftx-crypto-exchange-review-53664ac1198f)
*   [n 零审核](/coinmonks/ngrave-zero-review-c465cf8307fc)
*   [Bybit 交换审查](/coinmonks/bybit-exchange-review-dbd570019b71)
*   [3Commas vs Cryptohopper](/coinmonks/cryptohopper-vs-3commas-vs-shrimpy-a2c16095b8fe)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   [密码本交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)
*   最佳 [monero 钱包](https://blog.coincodecap.com/best-monero-wallets)
*   [莱杰纳米 s vs x](https://blog.coincodecap.com/ledger-nano-s-vs-x)
*   [bits gap vs 3 commas vs quad ency](https://blog.coincodecap.com/bitsgap-3commas-quadency)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   最佳[加密贷款平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [莱杰 Nano S vs 特雷佐 one vs 特雷佐 T vs 莱杰 Nano X](https://blog.coincodecap.com/ledger-nano-s-vs-trezor-one-ledger-nano-x-trezor-t)
*   [block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)vs Hodlnaut
*   Bitsgap 评论——一个轻松赚钱的加密交易机器人
*   为专业人士设计的加密交易机器人
*   [PrimeXBT 审查](/coinmonks/primexbt-review-88e0815be858) |杠杆交易、费用和交易
*   [Altrady 评论](https://blog.coincodecap.com/altrady-reivew)
*   [埃利帕尔泰坦评论](/coinmonks/ellipal-titan-review-85e9071dd029)
*   [SecuX Stone 评论](https://blog.coincodecap.com/secux-stone-hardware-wallet-review)
*   [BlockFi 评论](/coinmonks/blockfi-review-53096053c097) |从您的密码中赚取高达 8.6%的利息
*   [面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   [最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)
*   了解比特币的[最佳书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)

> [直接在您的收件箱中获得最佳软件交易](/coinmonks/newsletters/coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://medium.com/coinmonks/newsletters/coinmonks)