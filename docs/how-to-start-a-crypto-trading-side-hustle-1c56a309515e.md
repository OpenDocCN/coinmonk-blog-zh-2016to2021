# 如何开始加密货币的旁门左道

> 原文：<https://medium.com/coinmonks/how-to-start-a-crypto-trading-side-hustle-1c56a309515e?source=collection_archive---------0----------------------->

## 投资可能是一项耗时的活动。为什么不建立一个程序来告诉你什么时候交易？

![](img/8835d307c0c156eab5df1a8495cc93b2.png)

Photo by [Chris Liverani](https://unsplash.com/@chrisliverani?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

比特币最近的波动性，虽然它给了[*霍德勒*](https://en.wikipedia.org/wiki/Hodl) 灰色头发，但为活跃的加密货币投资者提供了一个合法的获利机会。随着 BTC 最近的价格飙升，很难否认世界上首屈一指的加密货币的成功。如果你曾经有进入加密货币投资的冲动，这篇文章将向你展示如何通过*算法，*使用一个叫做 Crypto Signal 的 Python 程序。

# 什么是加密信号？

有几十个电报集团出售加密交易信号订阅。这些基本上是私人电报组(或不和谐服务器等。)出售对其*信号*的订阅(即告诉你购买或出售加密货币的消息)。如果你能免费获得这些信号会怎么样？通过有限地使用命令行和一些非常基本的代码，我们能够在 Github 上的 C rypto-Signal 社区的帮助下建立我们自己的加密交易信号机器人。这个开源软件允许用户创建他们自己的信号，或者使用在 [Discord](https://discord.com/invite/MWTJVFf) 上活跃的加密信号社区中发现的成千上万贡献者创建的信号。

[http://gph.is/2nxnmRc](http://gph.is/2nxnmRc) via GIPHY

# 为什么是免费的

这个开发者社区免费提供他们易于使用的开源软件。我知道你在想什么。你在寻找“***”***的答案，因为人们不会把有价值的东西免费赠送出去。嗯，在这里:*安装运行这个软件，对每个人来说都不容易*。它需要一些命令行知识，使用基本代码的经验，以及使用一个健壮但有时笨拙的叫做 [Docker](https://docs.docker.com/get-docker/) 的程序。如果你对代码没有任何经验，那么本教程将帮助你完成这个过程——尽管需要一些辅助的“谷歌搜索”。如果你是一名程序员或者有任何编写或阅读代码的经验——安装、定制和运行这个程序将像打印“Hello，World”一样简单。

1.  **安装码头工人**

首先，我们必须下载并安装 [Docker](https://docs.docker.com/get-docker/) 。

2.**克隆回购**

在命令行中，使用以下命令克隆当前的加密信号 Github repo:

```
git clone [https://github.com/CryptoSignal/Crypto-Signal](https://github.com/CryptoSignal/Crypto-Signal)
```

3.**创建和定制 YML 文件**

您将要创建和定制的 config.yml 文件将包含您的加密信号的所有逻辑。如果这是你第一次设置你的加密信号机器人，你会想要设置你的 config.yml 文件，并进行最小的定制，以便测试你的机器人并让它运行。一旦你知道它应该运行，你可以回到你的 config.yml 文件，写一个更健壮的信号。要创建 config.yml 文件，只需在“crypto-signal”文件夹中创建一个新的文本文件，并将其命名为“config.yml”。

我们的配置文件(config.yml)基本上由两部分组成:(1)信号逻辑和(2)信号传输。前一部分规定了信号应该何时发送，后一部分规定了信号应该如何发送以及应该发送到哪里。例如，我可以规定，每当比特币(BTC) VWAP(成交量加权平均价格)通过 Discord webhook(我在下面使用的例子)越过当前价格时，我都希望得到通知。

from GIPHY

3a。**写 TA 交易逻辑**

根据[的暗号 Github](https://github.com/CryptoSignal/Crypto-Signal) :

> “我们大部分技术分析的基础加密信号是 [TA-Lib](https://ta-lib.org/index.html) ，这是一个始于 1999 年的开源技术分析项目。这个项目……是分析烛台数据最值得信赖的开源库之一”

你可以查看 TA-Lib 的文档，看看它们支持哪些指标和信号，看看它们在 [Tradingview](https://www.tradingview.com/) 上的实时图表上是什么样子。我将带您了解如何设置我们的机器人，每当 BTC 每小时的 VWAP 穿过每小时的收盘价时，它都会给我们一个通知。机器人将每 300 秒(5 分钟)检查一次交叉，如果发生了，就通知我们。

```
settings:
    log_level: INFO
    update_interval: 300
    market_pairs:
    - BTC/USD
```

我们将在 Bitmex 上追踪价格。你也可以跟踪其他主要交易所的价格，如比特币基地、北海巨妖、波兰交易所等。

```
exchanges:
    bitmex:
        required:
            enabled: true
```

在这里，我们在线人部分设置我们的 VWAP 设置。我们使用的是利用过去 13 个周期(小时)的价格和交易量计算的每小时 VWAP，我们的 OHLCV 线人只是给出了最后一个小时的收盘价:

```
informants
    vwap:
        - enabled: true
          signal:
            - vwap
          candle_period: 1h
          period_count: 13
    ohlcv:
        - enabled: true
          signal:
            - close
          candle_period: 1h
          period_count: 1
```

# 设置我们的交叉:

我们交易算法的逻辑如下:当当前小时收盘价穿越 13 期成交量加权平均价上方时，基础货币(BTC/美元)处于上升趋势，我们应该买入(做多)，当当前小时收盘价穿越 13 期 VWAP 下方时，基础货币处于下降趋势，我们应该卖出(做空)。 ***注:*** *根据你在哪个交易所交易，你可以也可以不卖空；选择交易所时要记住这一点，因为这可能会极大地改变你的整体回报。*

```
crossovers:
  std_crossover:
        - enabled: false
          alert_enabled: true
          alert_frequency: once
          key_indicator: ohlcv
          key_indicator_index: 0
          key_indicator_type: informants
          key_signal: close
          crossed_indicator: vwap
          crossed_indicator_index: 0
          crossed_indicator_type: informants
          crossed_signal: vwap
          indicator_label: 13 Hourly VWAP
```

3b。**设置通知程序**

有几种方法可以给我们自己发送通知。我个人使用 [Slack](https://api.slack.com/messaging/webhooks) 是因为它易于使用，但是你也可以使用 [Telegram](https://core.telegram.org/bots/webhooks) 或 [Discord](https://support.discord.com/hc/en-us/articles/228383668-Intro-to-Webhooks) 。

```
notifiers:
    slack:
        required:
            webhook: [your webhook goes here]
        optional:
            template: "{{analysis.config.candle_period}} [{{market}}] {{indicator_label}} is {{status}}! [{{exchange}}] ({{values}}){{ '\n' -}}"
```

4.**运行我们的机器人**

关键时刻到了。如果您在运行以下代码时收到一个错误，显示“exec: "com.docker.cli ":在$PATH 中找不到可执行文件”，请进入 Docker *首选项*选项卡，导航到*实验功能*选项卡，然后禁用*启用云体验。*

```
docker run --rm -v $PWD/config.yml:/app/config.yml shadowreaver/crypto-signal:master
```

***注*** :你只会在程序运行的时候接收信号，所以如果你想全天接收信号，你就需要让你的电脑全天开着。坦白地说，这是这个加密信号软件的最大限制因素。我曾经想过通过 AWS &在线运行程序，我试图让程序在我的 Raspberry Pi 上运行，但是无法成功安装 Docker，也无法单独安装所有需要的依赖项。让我知道，如果你有任何成功找到运行这个机器人 24/7 的替代方式。

# 需要考虑的事项:

*   在切换到实时交易之前，你可以在沙盒环境中测试你的信号和交易。大多数主要交易所都会有一个 testnet 沙盒交易所，除了使用假币之外，它拥有真实交易所的全部功能。

# 放弃

所有**投资策略**和**投资**都涉及损失风险。本文包含的任何内容都不应被理解为**投资**建议。对某项**投资的**过去或潜在表现的任何提及不是，也不应被解释为对任何特定结果或利润的建议或保证。

如果你喜欢这篇文章:

*   [关注](https://grsahagian.medium.com/)我的未来故事
*   在 [LinkedIn](https://www.linkedin.com/in/graham-sahagian/) 上与我联系
*   查看我的[个人 Instagram](https://www.instagram.com/traitor.joe/)

## 另外，阅读

*   最好的[密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [Deribit 审查](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和 Testnet
*   [FTX 密码交易所评论](/coinmonks/ftx-crypto-exchange-review-53664ac1198f)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   [密码本交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   最佳[加密贷款平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [莱杰 vs 特雷佐](/coinmonks/ledger-vs-trezor-best-hardware-wallet-to-secure-cryptocurrency-22c7a3fd391e)
*   [block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)vs Hodlnaut
*   Bitsgap 评论——一个轻松赚钱的加密交易机器人
*   为专业人士设计的加密交易机器人
*   [PrimeXBT 审查](/coinmonks/primexbt-review-88e0815be858) |杠杆交易、费用和交易
*   HaasOnline 评论享受九折优惠
*   Bitmex 保证金交易的白痴指南
*   [eToro 评论](/coinmonks/etoro-review-78807ddeb33c) |交易股票、密码、交易所交易基金、差价合约和商品
*   [Bitmex 高级保证金交易指南](/coinmonks/bitmex-advanced-margin-trading-guide-2270c195ce25?source=friends_link&sk=1d986cca731f5084b9a2db4a4bc4a7ad)
*   开发人员的最佳加密 API
*   [最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   顶级[比特币节点](https://blog.coincodecap.com/bitcoin-node-solutions)提供商
*   最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)
*   了解比特币的[最佳书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com?utm_source=coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://coincodecap.com?utm_source=coinmonks)