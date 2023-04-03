# 在 iOS 中创建以太坊钱包—第 1 部分

> 原文：<https://medium.com/coinmonks/create-an-ethereum-wallet-in-ios-part-1-1e2f215226b7?source=collection_archive---------0----------------------->

![](img/a86881ed1b4238f53dcd490ea24d0876.png)

Photo by [Launchpresso](https://unsplash.com/@launchpresso?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/blockchain?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

如今，区块链变得越来越受欢迎，在这十年中涌现了许多创业公司和应用程序。传统银行正试图引入区块链技术，并谨防失去营销机会。

在开始创建以太坊钱包之前，最好了解以太坊、POW/POS、智能合约等的基本工作流程。这里有一些参考资料可以帮助你赶上。

[](https://www.investopedia.com/terms/b/blockchain.asp) [## 区块链解释

### 一本帮助你理解什么是区块链以及工业如何利用它的指南。你可能遇到过…

www.investopedia.com](https://www.investopedia.com/terms/b/blockchain.asp) 

区块链系统中的机制有一些不同的概念，例如，帐户创建，你不必上传你的帐户信息到任何服务器，你需要的是生成密钥并存储在你的本地机器中。一旦您将记录添加到分类帐，您的帐户信息将同步到分散分配系统。你只需要这把钥匙，用来查询所有的记录。

我将在这篇文章中做以下事情，希望能帮助一些 iOS 工程师潜入[区块链](https://blog.coincodecap.com/what-is-blockchain-a-simple-guide-for-dummies/)的世界。

# 帐目

**加载现有账户**

1.  输入帐户私钥的十六进制字符串
2.  输入 12 个单词的助记短语

**生成、删除、导出账户**

# 平衡

**显示 ETH、戴、余额**

作为参考，您可以查看现有的 wallet，如[***【meta mask】***](https://metamask.io/)，它可以帮助您更快地了解它是如何工作的。

此外，我将使用[***rinke by***](https://www.rinkeby.io/#stats)测试网络进行此演示。Rinkeby 是基于以太坊的测试链。有几个以太坊 testnet 可以玩，比如 *Ropsten，Kovan，gorli 等。*

# 项目设置

这个项目安装了 Swift 5.1，并使用 Cocoapods 作为包管理器，如果你愿意，你可以随时更改为 *Carthage，*大多数区块链图书馆也支持 Carthage。

# 图书馆

该项目中使用了以下库:

***web 3s swift***

[](https://github.com/matter-labs/web3swift) [## matter-labs/web3swift

### web3swift 是您与以太网进行任何交互的工具箱。感谢您在……的所有支持

github.com](https://github.com/matter-labs/web3swift) 

有几个类似的库也提供了访问以太坊账户的能力，例如

[](https://github.com/BANKEX/web3swift) [## BANKEX/web3swift

### web3.js 功能的快速实现通过 JSON RPC 与远程节点进行⚡交互💭智能合同 ABI 分析…

github.com](https://github.com/BANKEX/web3swift) [](https://github.com/trustwallet/wallet-core) [## 信任钱包/钱包-核心

### Trust Wallet Core 是一个跨平台的库，它实现了所有…

github.com](https://github.com/trustwallet/wallet-core) 

Trust Wallet Core 是一个非常好的库，它也为它提供了非常好的可读文档！

[](https://developer.trustwallet.com/wallet-core) [## 钱包核心

### Trust Wallet Core 是一个跨平台的库，它实现了许多…

developer.trustwallet.com](https://developer.trustwallet.com/wallet-core) 

# **生成账户**

首先，让我们生成帐户:

```
**func** createAccount(name: String) **throws** -> Web3Wallet {**guard** **let** mnemonicsString = **try** BIP39.generateMnemonics(bitsOfEntropy: bitsOfEntropy) 
**else** { **throw** Web3ServiceError.noMnemonics }**guard** **let** keystore = **try** BIP32Keystore(mnemonics: mnemonicsString, password: password, mnemonicsPassword: "", language: .english) 
**else** { **throw** Web3ServiceError.noKeyStore }**guard** **let** address = keystore.addresses?.first?.address 
**else** { **throw** Web3ServiceError.noAddress }**let** keyData = **try** JSONEncoder().encode(keystore.keystoreParams)**let** mnemonics = mnemonicsString.split(separator: " ").map(String.init)**return** Web3Wallet(address: address, data: keyData, name: name, type: .hd(mnemonics: mnemonics))}
```

BIP39 是最常见的 seed 短语标准，seed 短语包括存储追回区块链基金所需的所有信息的单词列表，有关更多详细信息，您可以查看本文:

[](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) [## 比特币/bips

### BIP: 39 层:应用程序标题:生成确定性密钥的助记码作者:马雷克·帕拉蒂努斯·帕沃尔·鲁斯纳克…

github.com](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) 

这里有两种钱包，标准和高清*(分级确定性)*钱包。为了便于理解，在标准钱包中，用户必须保存私钥以与公钥配对，然后才能访问钱包。HD wallet 是新时代的数字钱包，它可以自动生成私有/公共地址的分层树状结构。

简而言之，在这个演示中，高清钱包是有**助记符**的钱包，标准钱包有**私钥**。

[](https://www.investopedia.com/terms/h/hd-wallet-hierarchical-deterministic-wallet.asp#:~:text=An%20HD%20Wallet%2C%20or%20Hierarchical,generate%20them%20on%20their%20own.) [## 分层确定性钱包—高清钱包

### 一个高清钱包，或分层确定性钱包，是一个新时代的数字钱包，自动生成…

www.investopedia.com](https://www.investopedia.com/terms/h/hd-wallet-hierarchical-deterministic-wallet.asp#:~:text=An%20HD%20Wallet%2C%20or%20Hierarchical,generate%20them%20on%20their%20own.) 

以下是我用过的钱包模型:

```
**struct** Web3Wallet: Equatable {**let** address: String**let** data: Data**let** name: String**let** type: WalletType}**enum** WalletType: Equatable {**case** normal**case** hd(mnemonics: [String])}
```

首先，我生成**助记符**并从中获取密钥库，这里有地址和

创建帐户/钱包实际上只是生成私钥或助记符，它不需要上传到任何地方。

然而，这意味着你必须小心谨慎地保存好私人密钥或助记符，一旦你丢失了它，帐户将永远无法恢复😟。此外，如果有人得到你的钥匙，他/她可以访问你的帐户，无论他们喜欢什么，你不能向任何人寻求帮助。

# **导入账户**

接下来，我们可以通过助记符或私钥恢复帐户，这取决于您想要恢复哪个钱包。

**通过私钥:**

```
**func** importAccount(by privateKey: String, name: String) **throws** -> Web3Wallet {**let** formattedKey = privateKey.trimmingCharacters(in: .whitespacesAndNewlines)**guard** **let** dataKey = Data.fromHex(formattedKey), **let** keystore = **try** EthereumKeystoreV3(privateKey: dataKey, password: password)
**else** { **throw** Web3ServiceError.noKeyStore }**guard** **let** address = keystore.addresses?.first?.address 
**else** { **throw** Web3ServiceError.noAddress }**let** keyData = **try** JSONEncoder().encode(keystore.keystoreParams)**return** Web3Wallet(address: address, data: keyData, name: name, type: .normal)}
```

**按助记符:**

```
**func** importAccount(by mnemonics: [String], name: String) **throws** -> Web3Wallet {**let** mnemonicsString = mnemonics.joined(separator: " ")**guard** **let** keystore = **try** BIP32Keystore(mnemonics: mnemonicsString, password: password, mnemonicsPassword: "", language: .english) 
**else** { **throw** Web3ServiceError.noKeyStore }**guard** **let** address = keystore.addresses?.first?.address 
**else** { **throw** Web3ServiceError.noAddress }**let** keyData = **try** JSONEncoder().encode(keystore.keystoreParams)**return** Web3Wallet(address: address, data: keyData, name: name, type: .hd(mnemonics: mnemonics))}
```

之前提到过，高清钱包只能通过助记符导入。

标准钱包使用私钥来创建密钥库，HD 钱包使用助记符来生成密钥库以访问钱包信息。

# 导出帐户

根据不同种类的钱包，导出的数据是不同的。HD 钱包，用户将收到*助记符*以备将来使用，而标准钱包是*私钥*代替。

```
**func** exportAccount(wallet: Web3Wallet) **throws** -> ExportedType {**let** keyStoreManager = **try** fetchKeyStoreManager(wallet: wallet)**guard** **let** ethereumAddress = EthereumAddress(wallet.address) 
**else** { **throw** Web3ServiceError.noAddress }**let** key = **try** keyStoreManager.UNSAFE_getPrivateKeyData(password: password, account: ethereumAddress).toHexString()**switch** wallet.type { **case** .normal: **return** .privateKey(key: key) **case** .hd(**let** mnemonics): **return** .mnemonics(mnemonics: mnemonics, key: key) }}
```

下面是我使用的导出类型:

```
**enum** ExportedType {**case** privateKey(key: String)**case** mnemonics(mnemonics: [String], key: String)}
```

分解代码，首先，我们需要根据钱包类型获取密钥库管理器:

```
**func** fetchKeyStoreManager(wallet: Web3Wallet) **throws** -> KeystoreManager {**switch** wallet.type {**case** .normal:**guard** **let** keystore = EthereumKeystoreV3(wallet.data) **else** { **throw** Web3ServiceError.noKeyStore }**return** KeystoreManager([keystore])**case** .hd:**guard** **let** keystore = BIP32Keystore(wallet.data) **else** { **throw** Web3ServiceError.noKeyStore }**return** KeystoreManager([keystore])}}
```

Standard one 需要 **EthereumKeystore** ，HD wallet 需要 **BIP32Keystore，**然后使用这个 Keystore 和地址来获得私钥。

理论上，从私钥中恢复一个短语是不可能的。在此演示中，助记符存储在安全的本地数据库中，更好的方法是要求用户输入密码，并使用用户密码加密种子短语，然后将其保存在密钥库中。

# 删除帐户

实际上，一旦账户信息有了记录，就没有办法删除，这是区块链的主要目的之一

> **无法改变的**

我在这里做的，只是简单地删除我为这个帐户存储的地址，如果用户想再次访问这个帐户，必须再次导入它😅

这是第一部分的全部内容，在第二部分，我将演示如何从以太坊测试网获得**以太**、**戴、**和**的天平。**

[](/coinmonks/create-an-ethereum-wallet-in-ios-part-2-9cb9baabfd8c) [## 在 iOS 中创建以太坊钱包—第 2 部分

### 关于第二部分已经推迟了一段时间，我在过去的 6 个月里一直很忙，所以让我们直接进入…

medium.com](/coinmonks/create-an-ethereum-wallet-in-ios-part-2-9cb9baabfd8c) 

希望这篇文章能帮助初学区块链的 iOS 工程师加入这项酷而神奇的技术🤩

本文中可能仍有一些不正确的地方，欢迎指正或与我讨论！

如果你觉得这篇文章有帮助，别忘了给我点掌声。

快乐编码，享受区块链技术🙂

> 如果你想联系我，请随时通过
> [Twitter](https://twitter.com/calvinchangtw) 或 [Linkedin](https://www.linkedin.com/in/calvin-chang-cc/) 联系我

> 另读:[最佳比特币硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)