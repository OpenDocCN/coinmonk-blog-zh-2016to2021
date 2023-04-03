# 基于 Polkadot 的构建:在副链和副线程之间进行选择

> 原文：<https://medium.com/coinmonks/building-on-polkadot-choosing-between-a-parachain-and-parathread-362d1c0e0102?source=collection_archive---------3----------------------->

![](img/74405b022ce9251c67758911ccbf6e76.png)

奇偶校验[最近宣布](/polkadot-network/rococo-v1-a-holiday-gift-to-the-polkadot-community-9d4da8049769)洛可可 V1 的发布，这是一个“副链测试网”,供项目和开发人员测试他们的副链，看看他们之间的互操作性如何。虽然这个版本关注的是副链，但是除此之外还有另外一个更有趣的选择:[副线程](https://wiki.polkadot.network/docs/en/learn-parathreads)。这些是我对他们的差异以及哪个选项最适合哪个项目的想法。

**免责声明:**我们处于一个高度动态的生态系统中，这是我们的优势，随着时间的推移，本文的内容可能会过时。虽然我会尽量保持最新的，你可能也想做自己的研究。

# 帕拉什么？

如果您对所有这些术语都不熟悉，让我在下面为您解释一下:

1.  副链是连接到波尔卡多特网络的区块链。它租用一个专用插槽，以便能够与其他链进行互操作。
2.  并行线程是…相同的。然而，它这样做并不需要租用插槽，而是在需要时为所使用的资源付费。

如果您来自云计算或后端开发领域，这可能有助于您将 parachains 想象为您租用的服务器，使用承诺的资源(您支付 6 个月或 1 年的使用费，或更多)，而不是无服务器功能。两者都允许你运行你自己的(分散的)应用程序，但是有他们自己的特性，因此有他们自己的计费系统。

说到计费，获取 parachain 或 parathread 的方式是非常不同的，当你在每一个选项之间进行选择时，你也需要了解这些。

通常，你可以通过赢得波尔卡多特连锁店提供的一个位置的拍卖来获得一条副链。由于提供的位置数量有限，而有如此多的项目希望获得一个位置，因此可以合理地预期投标具有重要的价值——因此，获得一个位置可能需要大量的财务资源；这就是为什么许多项目正在寻找机会，通过邀请他们的社区参与来众筹资金以获得他们的位置(例如，在代币的交换中)，更多细节见[这篇文章](https://polkadot.network/obtaining-a-parachain-slot-on-polkadot/)。

当涉及到并行线程时，其机制仍然有点不清楚，但是期望它们不需要任何类型的插槽是公平的。我们已经知道，一些 parachain 槽将被保留给专用于支持 parathreads 的链，这意味着专用链将支持 parathreads，并将它们的数据中继到 Polkadot 网络。

# 那么我应该用哪一个呢？

从我的角度来看，这可以归结为一个成本效益分析。

看起来获得一个 parachain 插槽将需要你获得相当数量的钱或者一个大的社区准备好帮助你来换取一些你会放弃的代币。然而，你将有一个 6 个月、一年甚至两年的时隙，并且不需要向网络支付任何其他费用来使用它的互操作性特性。如果你希望你的链需要产生大量的链间流量，拥有一个槽可以节省大量的成本。分散式金融平台(例如 [Acala](https://acala.network) )或非波尔卡多链桥(例如 [Darwinia](https://darwinia.network) )就是这种情况。

另一方面，如果您还没有资源或社区，或者如果您想测试您的想法，选择 parathread 可能是一个更好的解决方案。不仅如此，对于某些特定类别的项目，我认为使用 parachain 插槽是没有意义的，也就是说，大多数流量都发生在自己的网络上，不需要太多的互操作操作。对于以身份、社交网络、物联网或游戏等特定用例为目标的链来说，情况就是如此。

但请记住，选择其中一个选项并不意味着你不能在未来选择另一个。例如，一旦你槽到期或者你没有设法更新它，你可以选择将你的副链转换成副线程。反之亦然，你可以从副链开始，然后一旦经济上更有意义，或者一旦你有了一个更大的社区，或者一旦你的链以交易费或通货膨胀的形式产生了更多的收入，就转向副链；对于大多数现有的和即将到来的项目，这可能是一个最佳策略。

如果你喜欢这篇文章，或者如果它对你有帮助，请在这篇文章上鼓掌，帮助 Medium 算法推荐给更多的人。如果您有任何问题或意见，请在下面留下您的评论。

或者，请随时发送捐款 12 qzdid 7 pdkfeencv 15 wqpdvitr 6 DFC 6 I 4x su 35 jhniwazmx！❤️

Eliott Teissonniere 是一位学者、工程师和企业家。他曾是一家治理和加密公司[*bit nation*](https://bitnation.co/)*的 CTO，该公司于 2017 年获得联合国教科文组织* [*NetExplo 奖，在以太坊上制作了世界上第一个婚姻 dApp，并领导了*](https://en.unesco.org/news/netexplo-forum-celebrated-its-10th-edition) [*Nodle 的*](https://nodle.io/) *区块链和工程团队，他为这些团队共同发明了各种* [*待批他在分权治理和区块链方面的工作被各种媒体报道，如*](https://patents.google.com/?inventor=Eliott+Teissonniere) [*【解密】*](https://decrypt.co/16404/what-will-blockchain-look-like-in-2030-the-experts-speak)*[*coin telegraph*](https://cointelegraph.com/news/nodle-outgrows-stellar-begins-to-migrate-to-own-blockchain)*[*coin desk*](https://www.coindesk.com/iot-app-nodle-moves-from-stellar-blockchain-to-polkadot)*；并在各种备受瞩目的活动中发言，如巴黎区块链周、斯坦福区块链会议，以及与过去的欧洲代表一起在欧盟委员会就通过区块链追踪新冠肺炎的主题发表演讲。他是伯克利区块链加速器* *的* [*导师，并通过他的公司*](https://www.xcelerator.berkeley.edu/x-network)[*nucleus Studio*](https://nuclei.studio)*为各种加密和技术初创公司提供建议。他在 2020 年共同创立了* [*治理研究所*](https://governanceresearch.institute/) *，为公司、社区和政府研究新的治理模式。***

## **另外，阅读**

*   **[学习以太坊和 Web3 开发](http://blog.coincodecap.com/go/learn)**
*   **最好的[密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)**
*   **[3 商业评论](/coinmonks/3commas-review-an-excellent-crypto-trading-bot-2020-1313a58bec92)**
*   **[Pionex 回顾](/coinmonks/pionex-review-exchange-with-crypto-trading-bot-1e459d0191ea)**
*   **[AAX 交易所评论](/coinmonks/aax-exchange-review-2021-67c5ea09330c) |推荐代码、交易费用、利弊**
*   **[Deribit 审查](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和 Testnet**
*   **[FTX 密码交易所评论](/coinmonks/ftx-crypto-exchange-review-53664ac1198f)**
*   **[n 零审核](/coinmonks/ngrave-zero-review-c465cf8307fc)**
*   **[Bybit 交换审查](/coinmonks/bybit-exchange-review-dbd570019b71)**
*   **[3Commas vs Cryptohopper](/coinmonks/3commas-vs-pionex-vs-cryptohopper-best-crypto-bot-6a98d2baa203)**
*   **最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)**
*   **[密码本交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)**
*   **最佳 [monero 钱包](https://blog.coincodecap.com/best-monero-wallets)**
*   **[莱杰纳米 s vs x](https://blog.coincodecap.com/ledger-nano-s-vs-x)**
*   **[bits gap vs 3 commas vs quad ency](https://blog.coincodecap.com/bitsgap-3commas-quadency)**
*   **最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)**
*   **[最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)**
*   **最佳[加密贷款平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)**
*   **[莱杰纳米 S vs 特雷佐 one vs 特雷佐 T vs 莱杰纳米 X](https://blog.coincodecap.com/ledger-nano-s-vs-trezor-one-ledger-nano-x-trezor-t)**
*   **[block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)vs Hodlnaut**
*   **Bitsgap 评论——一个轻松赚钱的加密交易机器人**
*   **[Quadency Review](/coinmonks/quadency-review-a-crypto-trading-automation-platform-3068eaa374e1) -为专业人士打造的加密交易机器人**
*   **[硬币追踪评论](/coinmonks/cointracking-review-a-reliable-cryptocurrency-tax-software-5114e3eb5737)**
*   **[YouHodler 点评](/coinmonks/youhodler-4-easy-ways-to-make-money-98969b9689f2)**
*   **[埃利帕尔泰坦评论](/coinmonks/ellipal-titan-review-85e9071dd029)**
*   **[SecuX Stone 评论](https://blog.coincodecap.com/secux-stone-hardware-wallet-review)**
*   **[BlockFi 评论](/coinmonks/blockfi-review-53096053c097) |赚取高达 8.6%的加密利息**
*   **[共同规则审查](https://blog.coincodecap.com/coinrule-review-a-perfect-trading-bot)**
*   **[最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)**
*   **[加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱**
*   **最佳加密制图工具**
*   **了解比特币的[最佳书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)**

> **[直接在您的收件箱中获得最佳软件交易](/coinmonks/newsletters/coinmonks)**

**[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://medium.com/coinmonks/newsletters/coinmonks)**