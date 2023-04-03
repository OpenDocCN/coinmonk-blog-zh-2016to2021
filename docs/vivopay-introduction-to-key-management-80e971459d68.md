# VivoPay 密钥管理简介

> 原文：<https://medium.com/coinmonks/vivopay-introduction-to-key-management-80e971459d68?source=collection_archive---------8----------------------->

![](img/3c98252f731d089e3ef817849b8af00f.png)

VivoPay 是新的和谐一区块链的一个新的加密钱包。VivoPay 旨在方便刚接触加密的人使用。如果你知道如何使用 Square Cash 或 Venmo，你将能够使用 VivoPay。我们有大计划:很快，VivoPay 钱包将能够执行高效、隐私保护的[零知识证明(ZKP)智能合同](/@ronaldmannak/vivo-pay-a-zero-knowledge-payment-system-727997e4d25f)，为区块链提供与传统金融机构竞争所需的可扩展性和隐私。(我们还创建了与 VivoPay 无缝协作的 ZKP 开发者工具。我们将很快分享更多细节)

这篇博文是关于密钥管理的初级读本。

# 什么是密钥管理？

用户主要通过两种应用/网站与区块链互动:交易所和钱包。比特币基地和币安就是交易所的例子。钱包的例子有 Trust Wallet、MyEtherWallet、ZenGo 和比特币基地钱包。你可以在苹果应用商店和谷歌 Play 商店找到这些钱包。

虽然交易所主要关注交易，但钱包通常更关注支付和运行 dApps(分散式应用程序，在区块链上运行的应用程序)。人们用用户名和密码登录交易所，就像人们习惯于登录网上银行一样。然而，钱包的工作方式不同。新用户首先感到震惊的是，钱包不使用用户名和密码。相反，钱包使用密钥。

用户第一次打开钱包时，会创建一对新的随机密钥。一个密钥是你的*公钥*，类似于银行账号，你可以放心地把公钥分享给需要付钱给你的人。第二把钥匙是你的*私钥*。正如你可能已经猜到的那样，你需要保持这个密钥的私密性，因为这个密钥是用来从你的账户解锁交易的。

你可能会想:如果密钥对是随机生成的，有没有可能我的钱包生成的密钥和比特币发明者 Satoshi 或者以太坊创始人 Vitalik 的钱包完全一样？如果是这样，我能花掉他们钱包里的几百万甚至几十亿美元吗？令人惊讶的答案是:是的，是的。是的，理论上有可能随机生成 Satoshi 或 Vitalik 的钱包。是的，你可以花他们的钱。然而，区块链使用随机生成的密钥对而不是用户名和密码是有原因的。原因是可能的密钥数量是如此巨大，随机生成 Satoshi 的密钥的机会微乎其微。(可能的键的数量被恰当地称为“键空间”)。事实上，比特币的密钥空间是 1 后面跟着 77 个 0。如果你想生成所有可能的组合键(其中之一将是 Satoshi 的组合键)，使用目前最快的计算机将需要超过 5 亿年的时间。

随机生成的密钥对比用户名/密码组合安全得多。那么，为什么你的银行不使用随机生成的密钥对呢？

# 密钥对的用户友好性

您的银行不使用密钥对而不是用户名和密码是有充分理由的。原因是琴键长，无法记忆。这里有一个以太坊私钥的例子:8d a4 ef 21 b 864 D2 cc 526 dbdb 2 a 120 BD 2874 c 36 c 9d 0 a 1 FB 7 f 8 c 63d 7 f 7 a 8 b 41 de 8 f 这是密钥对的另一半，公钥:63 fac 9201494 f 0 BD 17 b 9892 b 9 fa E4 d 52 Fe 3 BD 377。

从技术上来说，公钥是从私钥数学推导出来的，所以你只需要记住私钥。记住 64 个随机字符显然不是你能要求用户做的事情。这是一个问题。如果用户不记得用户名和密码，他们将如何使用钱包？

(显而易见的)答案是需要存储私钥。它必须被安全地存储，因为任何拥有私钥的人都可以花掉存储在账户中的硬币。因此，钱包将私钥存储在磁盘上，并使用用户设置的密码对密钥进行加密。

有好消息也有坏消息:

*   坏消息是:密码可以被黑，尤其是短密码。如果攻击者可以访问包含密码加密的私钥的文件，攻击者可以按字母顺序遍历所有可能的密码，并最终找到密码，从而能够访问您的资金。
*   好消息是:获取文件是一个障碍。
*   更多好消息:第一次使用的用户通常钱包里不会有太多的钱，这使得他们不太可能成为目标。

大多数钱包开发者认为将私钥存储在密码加密文件中就足够了。(尽管在 VivoPay，我们认为我们可以做得更好，稍后会详细介绍)。

# 备份密钥

如果丢失了包含用私钥加密的文件的设备，会发生什么情况？好吧，如果你没有记住你的私人钥匙(你很可能没有)，你将无法再使用资金。

一个解决方案是将私钥写在纸上，并存储在安全的地方。然而，很容易拼错一个字符或数字。此外，如果你在钱包中存放不同的硬币，你可能会有多个唯一的密钥对(例如，如果你拥有比特币、以太坊、*和*和谐一号硬币)。

某个天才想出的巧妙解决方案由两部分组成:

1.  一个钱包可以生成一个单一的通用密钥，该密钥在数学上(*确定性地*)为许多不同的区块链派生出无限数量的密钥(“一个密钥统治所有的密钥”)。现在，你只需要写下*一个*键，而不是多个。这是一个胜利。
2.  单个通用密钥可以被编码为多个短的无缝随机单词。我们都知道如何拼写单词(或者至少我们大多数人都知道)，所以一个错别字很容易被人识别出来(不像私钥 8da4ef21b 中的错别字……)。这种编码密钥的方法被称为*助记短语*、*助记种子*、*种子短语*或*恢复短语*。一个短语通常是 12、18 或 24 个随机单词。一个恢复短语的例子是:“鸽子木材报价板年轻的健壮套件邀请塑料常规头骨历史。”是的，如果您使用此恢复短语在 Trust Wallet 或 VivoPay 中恢复钱包，您将生成一个有效的密钥对。事实上，两个钱包将生成完全相同的密钥对，这意味着两个钱包可以访问相同的资金。*注意:这应该是显而易见的，但只是以防万一:不要使用上面的示例恢复短语来创建或恢复钱包。你的资金不会安全。相反，让钱包为您生成新的密钥对。*

与以前相比，恢复短语的概念在用户体验方面是一个巨大的改进。然而，这个概念仍然很难向新用户解释，我亲眼看到人们是如何因为不理解恢复短语的重要性而丢失大量硬币的。这是一个需要解决的用户体验问题。

感兴趣 VivoPay 是怎么解决的？阅读[我们如何创造一个非常容易使用的加密钱包](https://medium.com/p/c5f5faa53b70/edit)。

# 接下来去哪里？

*   阅读[我们如何创建一个超级易用的加密钱包](https://medium.com/p/c5f5faa53b70/edit)
*   阅读[我们如何创建一个超级安全的加密钱包](/@ronaldmannak/how-we-created-an-insanely-secure-crypto-wallet-617917063a06)
*   阅读[vivo pay 如何利用 Secure Enclave 和 CryptoKit](/@ronaldmannak/how-vivopay-leveraged-the-secure-enclave-and-cryptokit-8f0adf865f99) 中的技术细节
*   阅读 vivo pay[零知识支付系统](/@ronaldmannak/vivo-pay-a-zero-knowledge-payment-system-727997e4d25f)的未来
*   下载 [VivoPayEncryption 演示 app](https://github.com/VivoPay/VivoPayEncryption) (需要 iOS 14 和 Xcode 12)。
*   报名参加 [VivoPay 更新](https://vivopay.me)

## 另外，阅读

*   最好的[密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [密码本交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)
*   [莱杰 vs 特雷佐](/coinmonks/ledger-vs-trezor-best-hardware-wallet-to-secure-cryptocurrency-22c7a3fd391e)
*   了解比特币的[最佳书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)
*   [3 商业评论](/coinmonks/3commas-review-an-excellent-crypto-trading-bot-2020-1313a58bec92)
*   [AAX 交易所评论](/coinmonks/aax-exchange-review-2021-67c5ea09330c) |推荐代码、交易费用、利弊
*   [Deribit 审查](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和 Testnet
*   FTX 密码交易所评论
*   [n 零审核](/coinmonks/ngrave-zero-review-c465cf8307fc)
*   [Bybit 交换审查](/coinmonks/bybit-exchange-review-dbd570019b71)
*   [3Commas vs Cryptohopper](/coinmonks/cryptohopper-vs-3commas-vs-shrimpy-a2c16095b8fe)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   最佳 [monero 钱包](https://blog.coincodecap.com/best-monero-wallets)
*   [莱杰 nano s vs x](https://blog.coincodecap.com/ledger-nano-s-vs-x)
*   [bits gap vs 3 commas vs quad ency](https://blog.coincodecap.com/bitsgap-3commas-quadency)
*   [莱杰纳米 S vs 特雷佐 one vs 特雷佐 T vs 莱杰纳米 X](https://blog.coincodecap.com/ledger-nano-s-vs-trezor-one-ledger-nano-x-trezor-t)
*   [BlockFi 与摄氏度的关系](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)与 Hodlnaut 的关系
*   [Bitsgap 评论](/coinmonks/bitsgap-review-a-crypto-trading-bot-that-makes-easy-money-a5d88a336df2)——一个容易赚钱的秘密交易机器人
*   [第四次审查](/coinmonks/quadency-review-a-crypto-trading-automation-platform-3068eaa374e1)——一个为专业人士制造的秘密交易机器人
*   [PrimeXBT 审核](/coinmonks/primexbt-review-88e0815be858) |杠杆交易、费用和契约
*   [埃利帕尔泰坦评论](/coinmonks/ellipal-titan-review-85e9071dd029)
*   [赛克斯石材回顾](https://blog.coincodecap.com/secux-stone-hardware-wallet-review)
*   [BlockFi Review](/coinmonks/blockfi-review-53096053c097) |获取高达 8.6%的加密利息