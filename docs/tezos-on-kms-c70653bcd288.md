# KMS 的泰佐斯

> 原文：<https://medium.com/coinmonks/tezos-on-kms-c70653bcd288?source=collection_archive---------5----------------------->

由[基弗·泰勒](https://twitter.com/keefertaylor)和[卢克·扬布拉德](https://twitter.com/lukeyoungblood)

今天，我们开源了两个库，用于处理亚马逊网络服务的[密钥管理系统](https://aws.amazon.com/kms/)(“AWS KMS”)中存储的 [Tezos](https://tezos.com) 密钥。这些工具提供了一个安全的抽象概念，允许开发人员安全地使用存储在云中的热键。

![](img/783444e972cf7143291b894a65ac22ba.png)

Photo by [Jan Antonin Kolar](https://unsplash.com/@jankolar?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 关于 KMS

在讨论新图书馆之前，一些关于 KMS 的背景可能是有用的。

AWS KMS 是一个按需提供硬件安全模块(HSM)的云解决方案。HSM 是硬连线的计算机硬件，不允许提取密钥。AWS KMS 在多个地理区域提供对这些密钥的冗余和安全访问，同时防止恶意员工或恶意攻击者提取或窃取密钥。

KMS 最近开始支持 secp256k1 签名弯曲，这是用于 [Tezos](https://blog.coincodecap.com/tezos-blockchain-a-simple-overview-for-beginners) 和其他加密货币。这一发展使得 AWS KMS 成为保护需要在线、高度可用和安全的密钥的诱人解决方案。最重要的是，这项服务很便宜，存储和使用一把钥匙每月只需几美元。

# 在先驱基础设施中使用

[Harbinger price Oracle](https://github.com/tacoinfra/harbinger)(一个安全的连锁价格源)广泛使用 AWS KMS。Harbinger 数据馈送的签名者将他们的密钥存储在 KMS，在那里他们被用于签名数据馈送。Tezos mainnet 上 Harbinger 数据的[海报存储资金和签名，每小时支付一次手术费用。出于好奇，](https://tzstats.com/tz2L9474hvA2EXyNLi4u3YnqrKVf3GzeZ8tb)[基弗·泰勒](https://twitter.com/keefertaylor)和[卢克·扬布拉德](https://twitter.com/lukeyoungblood)在 [TezTalks 11](https://www.youtube.com/watch?v=jaVnBujxd8w&t=66s) 中广泛谈论了 KMS 是如何被运用在先兆系统中的。

这两种服务都保证了不小的价值。在签名者的情况下，恶意用户可以改变价格馈送，潜在地影响 DeFi 工具的结果，导致任意高的损失金额。在发帖人的案例中，活资金被放在一个热门的钱包里。这两种服务都要求密钥持续在线(在签名者的情况下，根据需要对数据进行签名，或者在发帖者的情况下，为甲骨文的更新付费)。KMS 代表了一种安全的解决方案，允许这两种服务同时运行。

# 广义 KMS 图书馆

事实证明，KMS 是一个非常有用的存储 Tezos 密钥的服务，我们认为其他开发人员可能会发现将这个基础设施用于他们自己的 DeFi 项目或运行在 Tezos 上的服务的价值。我们已经从 Harbinger 基础设施中归纳和提取了两个 TypeScript 包以供重用。

## 泰佐斯-KMS

第一个包， [Tezos-KMS](https://www.npmjs.com/package/@tacoinfra/tezos-kms) ，目标是 Tezos 开发者，他们需要一个安全的解决方案来存储密钥和签署任意字节。

该库提供了一个 KMS 密钥的包装器，允许检索公钥、公钥散列，并允许密钥对任意字节进行签名。这个库是模块化的，使用简单，易于理解。下面是如何开始的一个简单示例:

```
const kmsClient = new TezosKmsClient(awsKeyId, awsRegion)await kmsClient.getPublicKey() // sppk…
await kmsClient.getPublicKeyHash() // tz2…await kmsClient.signOperation(Buffer.from(‘deadbeef’, ‘hex’)) // <bytes>await kmsClient.signOperationBase58(Buffer.from(‘deadbeef’, ‘hex’)) // spsig…
```

## KMS 理事会

第二个包， [Conseil-KMS](https://www.npmjs.com/package/@tacoinfra/conseil-kms) ，为使用 [Cryptonomic 的 ConseilJS](https://github.com/cryptonomic/conseiljs) 提供即插即用功能。ConseilJS 为签名操作(称为`Signer` s)和使用密钥(称为`Keystore` s)提供了模块化接口。

KMS 理事会提供了一个由存储在 KMS 的密钥支持的`Signer`和`KeyStore`的实现。使用 Conseil 的开发人员只需要实例化这些新的实现，并将它们提供给 ConseilJS 用于操作。这里有一个简单的例子:

```
const signer = new KmsSigner(awsKeyId, awsRegion)
const keystore = KmsKeyStore.from(awsKeyId, awsRegion)// This is a vanilla Conseil invocation with no 
// modification requiredconst result = await TezosNodeWriter.sendTransactionOperation(
 “https://rpctest.tzbeta.net", 
 signer, 
 keystore, 
 ‘tz1RVcUP9nUurgEJMDou8eW3bVDs6qmP5Lnc’, // Recipient
 500000, // Amount, in mutez
 1500 // Fee, in mutez
)
```

# 结论

AWS KMS 为需要高价值服务的热、安全和冗余密钥访问的企业级 dApps 和服务提供了令人信服的安全解决方案。

这些新的库为所有 Tezos 开发者提供了通用的、可重用的接口。我们期待着继续为 Tezos 生态系统构建有用的 DeFi 原语和基础设施，并欢迎贡献和反馈。开发人员可以在各自的 GitHub repos 中查看源代码、派生或贡献给这些库:

*   https://github.com/tacoinfra/tezos-kms
*   **康塞尔 https://github.com/tacoinfra/conseil-kms KMS:**T2

## 另外，阅读

*   最好的[密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [密码本交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   最佳[加密贷款平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)
*   莱杰 vs 特雷佐
*   了解比特币的[最佳书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)
*   [3 商业评论](/coinmonks/3commas-review-an-excellent-crypto-trading-bot-2020-1313a58bec92)
*   [AAX 交易所评论](/coinmonks/aax-exchange-review-2021-67c5ea09330c) |推荐代码、交易费用、利弊
*   [Deribit 审查](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和 Testnet
*   [FTX 密码交易所评论](/coinmonks/ftx-crypto-exchange-review-53664ac1198f)
*   [n 零审核](/coinmonks/ngrave-zero-review-c465cf8307fc)
*   [Bybit 交换审查](/coinmonks/bybit-exchange-review-dbd570019b71)
*   [3Commas vs Cryptohopper](/coinmonks/cryptohopper-vs-3commas-vs-shrimpy-a2c16095b8fe)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   最佳 [monero 钱包](https://blog.coincodecap.com/best-monero-wallets)
*   [莱杰 nano s vs x](https://blog.coincodecap.com/ledger-nano-s-vs-x)
*   [Bitsgap vs 3 commas vs quad ency](https://blog.coincodecap.com/bitsgap-3commas-quadency)
*   [莱杰纳米 S vs 特雷佐 one vs 特雷佐 T vs 莱杰纳米 X](https://blog.coincodecap.com/ledger-nano-s-vs-trezor-one-ledger-nano-x-trezor-t)
*   [block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)vs Hodlnaut
*   bits gap review——一个轻松赚钱的加密交易机器人
*   为专业人士设计的加密交易机器人
*   [PrimeXBT 审查](/coinmonks/primexbt-review-88e0815be858) |杠杆交易、费用和交易
*   [埃利帕尔泰坦评论](/coinmonks/ellipal-titan-review-85e9071dd029)
*   [赛克斯·斯通评论](https://blog.coincodecap.com/secux-stone-hardware-wallet-review)
*   [BlockFi 评论](/coinmonks/blockfi-review-53096053c097) |赚取高达 8.6%的加密利息