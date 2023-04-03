# Hyperleder 面料的秘密瑰宝——fab token

> 原文：<https://medium.com/coinmonks/the-secret-gem-of-hyperleder-fabric-fabtoken-aa5c37159247?source=collection_archive---------0----------------------->

![](img/4496587e623833ff984d03b826c5c9e6.png)

Credit: cryptocoin.news

如果您曾想过未来的经济将被象征性化，或者我们称之为象征性经济，欢迎来到区块链世界，特别是 Hyperledger Fabric，确切地说，是 hyper ledger Fabric 2.0 版(alpha)。

因为 Hyperledger Fabric 版(alpha)有一个新功能，叫做 Fabtoken，它允许您创建本地加密货币或硬币。我们中的许多区块链行业人士都知道，Ethereum 拥有强大的 ERC20 标准，允许在 Ethereum 区块链网络上创建加密硬币。现在，有了 Fabtoken，使用 Hyperledger Fabric 的公司和开发人员可以实现同样的目标。

这篇文章将带你走过法布里肯。

**Fabtoken 平台软件/ Fabric 2.0 (alpha)安装**

首先，我们先下载并安装 Fabric 2.0 alpha 版。

使用以下网址下载并安装它。警告:确保您的 Linux/ubto 操作系统没有安装其他版本的 Hyperledger Fabric(虽然可以通过指定它所涉及的版本来运行 Fabric，但我不想在这里引入不必要的复杂性……)，也就是说，彻底安装 Fabric 2.0 alph，同时确保您有 Docker 和 Node 这样的先决条件。JS 已经安装。

```
**curl -sSL** [**http://bit.ly/2ysbOFE**](http://bit.ly/2ysbOFE) **| bash -s — 2.0.0-alpha 2.0.0-alpha 0.4.15**
```

**晶圆代名词的核心功能**

让我们先对 Fabtoken 有一个清晰的概念理解。

Fabtoken 提供以下核心功能:

*   创建新的加密硬币
*   将硬币从一个用户(所有者)转移到另一个用户
*   列出交易记录
*   兑换硬币

在大多数情况下，我们最常使用前三个函数。

**晶圆代工厂如何运作**

一旦安装了 Fabric 2.0 alpha，您就可以使用“docker images”进行快速验证。

现在通过发出以下命令转到 Fabtoken 目录:

**CD $ HOME/fabric-samples/fab token**

让我解释一下，Fabtoken 期望一个 Fabric 网络能够运行，它附带了一个名为 fabtoken.js 的示例节点应用程序以及一个名为“startFabric.sh”的 shell 脚本，该脚本位于当前目录中。它做了一些重要的事情，首先，它启动“basic-network ”,然后，它进入“javascript”目录，然后，它运行“npm install ”,然后，它运行一个 fabtoken 演示。

我强烈建议您打开这个脚本来阅读它。

不用多说，让我们通过发出以下命令来启动 startFabric.sh 脚本:

**。/startFabric.sh**

所以，现在你准备好和法布里肯一起踢屁股了。首先，让我们为用户 1 创造 1000 个金币。

以下是执行此操作的命令:

**节点晶圆代币发行用户 1 金币 1000**

您应该会看到如下屏幕:

```
 — — fabtoken.js — startSetting up client side network objectsCreated client side object to represent the channelCreated client side object to represent the peerCreated client side object to represent the ordererSuccessfully setup client sideToken arg: goldcoinToken arg: 1000Start issue token operationStart token issue with args goldcoin,1000End issue token operation, returns{ status: ‘SUCCESS’, info: ‘’ } — — fabtoken.js — end
```

很明显，您看到了“成功”这个词！

太好了。

接下来，我们想通过列出来查看一下。以下是命令:

**节点 fabtoken 列表 user1**

您应该会看到如下屏幕:

```
 — — fabtoken.js — startSetting up client side network objectsCreated client side object to represent the channelCreated client side object to represent the peerCreated client side object to represent the ordererSuccessfully setup client sideStart list token operationEnd list token operation, returns[{ id:{ tx_id: ‘e0b8a7ce6b248b8733ac7659c32a45b04a571de2548e371ada810a1e8bcf8eac’,index: 0 },type: ‘goldcoin’,quantity: ‘1000’ } ] — — fabtoken.js — end
```

不错。

接下来，比方说，我们(用户 1)非常慷慨，所以我们决定送 10 枚新铸造的金币给用户 2。因此，我们发出以下命令:

**节点 fabtoken 传送 user 1 user 2 10 e0b 8 a7 ce 6b 248 b 8733 AC 7659 c 32 a 45 b 04 a 571 de 2548 e 371 ada 810 a1 E8 BC F8 EAC 0**

(确保键入并复制/粘贴上述命令到同一行)

让我解释一下它的语法。这意味着从所有者用户 1 转移到用户 2，数量为 10，交易 id 和索引来自列表操作。

您可能想知道为什么我们要在命令中添加隐晦的 E0 b 8 a 7 ce 6 b 248 b 8733 AC 7659 c 32 a 45 b 04 a 571 de 2548 e 371 ada 810 a 1 e 8 BCF 8 EAC，然后添加 0。

嗯，它们是有意义的，隐含数据是来自 list 函数的事务 id，0 是这种事务的索引。

您应该会看到如下屏幕:

```
 — — fabtoken.js — startSetting up client side network objectsCreated client side object to represent the channelCreated client side object to represent the peerCreated client side object to represent the ordererSuccessfully setup client sideToken arg: user2Token arg: 10Token arg: e0b8a7ce6b248b8733ac7659c32a45b04a571de2548e371ada810a1e8bcf8eacToken arg: 0Start transfer token operationStart token transfer with args 10,e0b8a7ce6b248b8733ac7659c32a45b04a571de2548e371ada810a1e8bcf8eac,0End transfer token operation, returns{ status: ‘SUCCESS’, info: ‘’ }
```

接下来，让我们验证用户 2 确实从用户 1 那里得到了 10 金币，因此，我们发出以下命令:

**节点 fabtoken 列表用户 2**

哇，太好了！我们看到以下屏幕:

```
 — — fabtoken.js — startSetting up client side network objectsCreated client side object to represent the channelCreated client side object to represent the peerCreated client side object to represent the ordererSuccessfully setup client sideStart list token operationEnd list token operation, returns[{ id:{ tx_id: ‘7e772f9d2e9e94a5c06e3ff2a62d13a41591b7d47daa9886c842aed6dd6d6582’,index: 0 },type: ‘goldcoin’,quantity: ‘10’ } ] — — fabtoken.js — end
```

是不是很酷？

是的，你说，但是用户 1 和用户 2 对我来说没有意义，我是萨姆，我有一个朋友，唐，我想为我的钻石生意创造一些硬币，并把一些送给我的朋友唐。那么，怎么做呢？

在我向你们展示之前，让我先向你们展示它的样子。

首先创造硬币。

**node fabtoken3 一期 sam diamondcoin 1000**

请注意，我使用的是“fabtoken3”而不是“fabtoken”，其次是“sam”而不是“user1”。

让我们看看这里有什么:

```
 — — fabtoken.js — startSetting up client side network objectsCreated client side object to represent the channelCreated client side object to represent the peerCreated client side object to represent the ordererSuccessfully setup client sideToken arg: diamondcoinToken arg: 1000Start issue token operationStart token issue with args diamondcoin,1000End issue token operation, returns{ status: ‘SUCCESS’, info: ‘’ } — — fabtoken.js — end
```

事实上，1000 枚钻石硬币是为山姆创造的。

接下来，我们来验证一下:

**节点 fabtoken3 列表 sam**

```
 — — fabtoken.js — startSetting up client side network objectsCreated client side object to represent the channelCreated client side object to represent the peerCreated client side object to represent the ordererSuccessfully setup client sideStart list token operationEnd list token operation, returns[ { tx_id: ‘837ac2e3bd7a763f3a11d5a3d32822dac0a215a98f5ee7849d87111b03f631c6’,type: ‘diamondcoin’,quantity: ‘1000’ } ] — — fabtoken.js — end
```

现在，让我们给唐 50 枚钻石硬币。

**node fab token 3 transfer Sam don 50 837 AC 2 E3 BD 7763 F3 a 11d 5 a3 d 32822 DAC 0a 215 a 98 f 5 ee 7849d 87111 b 03 f 631 c 6 0**

```
 — — fabtoken.js — startSetting up client side network objectsCreated client side object to represent the channelCreated client side object to represent the peerCreated client side object to represent the ordererSuccessfully setup client sideToken arg: donToken arg: 50Token arg: 837ac2e3bd7a763f3a11d5a3d32822dac0a215a98f5ee7849d87111b03f631c6Token arg: 0Start transfer token operationStart token transfer with args 50,837ac2e3bd7a763f3a11d5a3d32822dac0a215a98f5ee7849d87111b03f631c6,0End transfer token operation, returns{ status: ‘SUCCESS’, info: ‘’ } — — fabtoken.js — end
```

现在，让我们看看唐是否真的收到了 50 枚钻石硬币。

**节点 fabtoken3 列表唐**

我们会看到下面的屏幕:

```
 — — fabtoken.js — startSetting up client side network objectsCreated client side object to represent the channelCreated client side object to represent the peerCreated client side object to represent the ordererSuccessfully setup client sideStart list token operationEnd list token operation, returns[{ id:{ tx_id: ‘74316bd91757907e9c878a78d725b8c9f605b505ccd1801e8afd407bbd8b53b3’,index: 0 },type: ‘diamondcoin’,quantity: ‘50’ }] — — fabtoken.js — end
```

宾果游戏

唐得到了 50 枚钻石硬币！

现在，大家都开心了。希望你也一样。

**最好有什么**

以下是一些想法。

从功能上来说，希望知道用户的特定加密硬币的余额。

比方说，我的用户 1 有 8 个事务，“节点 fabtoken 列表用户 1”将列出所有这 8 个事务，这很有用。与此同时，如果我想快速找出 mycoin 的 user1 的余额，我将无法做到。因此，如果我们有另一个 fabtoken 函数，比如“balance”(暂时先不管它是动词还是名词)，那么，

***“node fabtoken balance user1***”将返回 user1 的 mycoin 总数，前提是 user 1 只有一种类型的硬币。

这带来了一个相关的项目。如果用户 1 拥有多种类型的硬币会怎样？

目前，“节点 fabtoken 列表用户 1”将列出属于用户 1 的所有类型的硬币。对于“列表”函数来说，希望具有另一个用于“类型”的可选参数，因此，例如，“ ***节点 fabtoken 列表 user1 mycoin*** ”将仅返回 mycoin 的所有交易，即使 user1 有其他硬币。

下面是 Fabtoken 为拥有多枚硬币的用户工作的示例。

**节点 fabtoken3 列表 sam**

```
 — — fabtoken.js — startSetting up client side network objectsCreated client side object to represent the channelCreated client side object to represent the peerCreated client side object to represent the ordererSuccessfully setup client sideStart list token operationEnd list token operation, returns[{ id:{ tx_id: ‘74316bd91757907e9c878a78d725b8c9f605b505ccd1801e8afd407bbd8b53b3’,index: 1 },type: ‘diamondcoin’,quantity: ‘950’ },{ id:{ tx_id: ‘7e772f9d2e9e94a5c06e3ff2a62d13a41591b7d47daa9886c842aed6dd6d6582’,index: 1 },type: ‘goldcoin’,quantity: ‘990’ } ] — — fabtoken.js — end
```

比方说，您只想列出 sam 在 diamondcoin 的所有交易，目前您无法做到这一点。

如果你尝试“**node fab token 3 list Sam diamond coin**

“diamondcoin”的最后一个参数将被忽略。

出于同样的原因，对于所提出的“balance”函数的新函数，也希望具有用于“type”的可选参数，因此，例如，

“节点 fabtoken 余额用户 1”将返回用户 1 拥有的所有硬币的总余额，而

**“node fab token balance user1 mycoin**”将返回仅属于 user 1 的 my coin 的聚合，这种情况可能更实际。

还有一点就是**安全**。对于许多加密货币平台，如 EOS 或 eosio，它们总是要求每笔交易都有一个私钥，这很好。此外，私钥不应存储在文件系统中。然后，即使有人闯入你的帐户，他们也无法窃取你的硬币，他们也无法破解你的私钥。

希望你喜欢这篇文章。

如果你想节省自己的时间，避免挫折，并想知道我是如何创建用户萨姆和唐，以及进一步，如何添加额外的用户，说，安妮，杰克等。只需很少的费用，我就可以把我的代码和指令发给你。

如果你觉得你从这篇文章中受益匪浅，请不要忘记鼓掌，最好在下面给小费。

BTC:1 etjj G7 D1 pq 4k 86 tmk ddg 7 USM 2 destxlf 1

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)