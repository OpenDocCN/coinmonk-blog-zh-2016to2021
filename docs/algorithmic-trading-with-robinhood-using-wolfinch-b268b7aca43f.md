# 使用 Wolfinch 的 Robinhood 算法交易

> 原文：<https://medium.com/coinmonks/algorithmic-trading-with-robinhood-using-wolfinch-b268b7aca43f?source=collection_archive---------1----------------------->

![](img/a8217d3208b86c9ccaf2763b42dd4a01.png)

Photo by [Austin Distel](https://unsplash.com/@austindistel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

[Wolfinch](https://www.wolfinch.com) 是一个用 python 编写的开源算法交易平台。它允许用户创建交易策略并自动执行交易。Wolfinch 支持股票市场和加密货币市场的算法交易。它支持多个经纪人和交易所进行交易。

你可以在这里 ***阅读更多关于安装和使用 Wolfinch bot [***的信息。*** Wolfinch 项目是托管在 github 中](/@joe.cet/wolfinch-introduction-to-the-friendly-trading-bot-fe9281825e59) [**这里**](https://github.com/ldmonko/wolfinch) 。***

现在让我们把重点放在使用 Wolfinch 在 Robinhood 上进行算法交易上。这主要有两个步骤。

# 设置 Wolfinch

Wolfinch 配置是通过 YAML 配置文件完成的。有大量的配置选项可用于调整和优化机器人的各种功能。配置文件分为多个部分，用于配置交换、策略、各种限制等。

下面是一个示例配置文件。

YAML 配置文件本身非常直观。不过，你可以在这里找到更多细节[](/@joe.cet/wolfinch-introduction-to-the-friendly-trading-bot-fe9281825e59)****。****

**上面的示例配置文件非常详尽地展示了配置选项。实际上，配置文件可以简单得多。唯一与 Robinhood 相关的部分是顶部的交换配置。这是配置股票符号及其限制的地方。此外，它需要一个特定于 exchange 的配置文件来配置罗宾汉访问详细信息。**

# **设置罗宾汉身份**

**Wolfinch 根据配置的策略做出交易决定。然而，交易最终是在交易所或经纪人那里进行的。**

**由于我们使用 Robinhood 进行交易，我们需要为 Wolfinch 配置 Robinhood 访问权限。Wolfinch 的 exchange 配置是通过一个简单的 YAML 配置文件完成的。出于安全原因，这是一个独立于主 bot 配置文件的文件。**

**以下是 Robinhood 的示例 exchange 配置文件。**

**Wolfinch 无缝访问 Robinhood APIs 需要 2FA。要启用 2FA，您可以转到您的 Robinhood Web 应用程序。进入设置，打开 2FA，选择“认证应用”，点击“扫描不了？”，并复制 16 个字符的二维码。更多详情 [**此处**](https://robinhood.com/us/en/support/articles/twofactor-authentication/) **。****

***注意:Robinhood 要求最低 2.5 万美元的余额，以避免模式日交易者(PDT)标记。你可以考虑这一点，并相应地制定你的策略，要么避免 PDT，要么接受它。***

# **实施细节**

**对于那些好奇的人来说，这里有一些关于 Robinhood 交换模块的实现细节。**

**Wolfinch Robinhood 模块使用 Robinhood APIs 进行交易和查找订单状态以及类似的任务。大多数交换交互都使用著名的 RH python 客户端，这里是 https://github.com/robinhood-unofficial/pyrh 的**

**对于实时股票更新和历史烛台数据，雅虎金融 API 正在使用。Ticker updates 使用 websocket 端点进行实时订阅。**

# ****结束语****

**Robinhood 和 Wolfinch 为成功的算法交易系统提供了一个完美的组合。找到一个有效的策略是任何成功的算法交易系统背后真正的创造性工作。Wolfinch 提供了合适的平台和必要的工具来实现这一无缝的探索。
交易愉快！**

## **另外，阅读**

*   **最好的加密交易机器人**
*   **最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)**
*   **最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)**
*   **[最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)**
*   **[unis WAP 最佳钱包](/coinmonks/best-wallets-to-use-uniswap-e91a6385d9e8)**
*   **最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)**
*   **[莱杰 vs 特雷佐](/coinmonks/ledger-vs-trezor-best-hardware-wallet-to-secure-cryptocurrency-22c7a3fd391e)**
*   **[顶级 DeFi 项目](/coinmonks/defi-future-10-promising-projects-in-the-defi-world-ff2b697ab006)**
*   **Bitsgap 评论——一个轻松赚钱的加密交易机器人**
*   **为专业人士设计的加密交易机器人**
*   **[3commas Review](https://blog.coincodecap.com/3commas-review-an-excellent-crypto-trading-bot) |一款优秀的密码交易机器人**
*   **[3Commas vs Cryptohopper](/coinmonks/cryptohopper-vs-3commas-vs-shrimpy-a2c16095b8fe)**
*   **Bitmex 上的[保证金交易的白痴指南](/coinmonks/the-idiots-guide-to-margin-trading-on-bitmex-dbbd7742c6fc?source=friends_link&sk=7bfa99d2a181142510c8442c8ddb0786)**
*   **[加密摇摆交易的权威指南](/coinmonks/the-definitive-guide-to-crypto-swing-trading-7e4af6496d4d?source=friends_link&sk=70448050bd9323b42f63bfc0bb1e60d1)**
*   **[Bitmex 高级保证金交易指南](/coinmonks/bitmex-advanced-margin-trading-guide-2270c195ce25?source=friends_link&sk=1d986cca731f5084b9a2db4a4bc4a7ad)**
*   **[面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)**
*   **[加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱**
*   **顶级[比特币节点](https://blog.coincodecap.com/bitcoin-node-solutions)提供商**
*   **最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)**

> **[直接在您的收件箱中获得最佳软件交易](https://coincodecap.com?utm_source=coinmonks)**

**[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://coincodecap.com?utm_source=coinmonks)**