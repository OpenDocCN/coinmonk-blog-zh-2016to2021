# 构建不可替代的代币销售智能合同

> 原文：<https://medium.com/coinmonks/building-a-non-fungible-token-sales-smart-contract-6573350d5a33?source=collection_archive---------3----------------------->

![](img/4909ecaf24c2503b017c07dccbca965e.png)

CryptoArte.io Painting #8118

## 销售不可替代令牌和 ERC721 标准

如果你正在做一个以太坊 [NFT (ERC721)项目，](http://erc721.org/)你可能已经注意到了[标准](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-721.md)描述了通过**safetransform()**等函数转移令牌的机制，并且，虽然有些函数是**可支付的，**该标准省略了关于如何实现实际出售令牌以换取以太或其他令牌的机制的任何细节。虽然这显然是有意为之且经过深思熟虑的，但您可能仍然需要想出一种方法来出售代币。

## 不可替代的代币市场

做到这一点的一个方法是利用现有的市场，如 [OpenSea](https://www.opensea.io) 。这确实是一个很好的选择，因为:

*   您不必编写和维护您自己的销售合同和内置于您自己的 dApp 中的销售功能，
*   你从他们的社区获得内在的流动性
*   您仍然可以通过他们的 SDK 和 API 获得极大的灵活性。

但是，在某些情况下，您可能希望拥有更多控制权，并实现复杂的用户体验或定价策略，这需要您编写自己的智能合约。CryptoArte 就是这种情况:虽然我们与 OpenSea 集成，并通过它们定期销售和拍卖画作，但我们也希望在买家已经在我们的 dApp 上查看画作时提供简化的用户体验，并具有随时轻松试验定制定价策略的灵活性。

## 一个简单的 NFT 销售合同模型

因此，我们继续在 dApp 中构建我们自己的销售合同及其相应的功能。虽然它可能会随着时间的推移而发展，但我们的第一个实现 [CryptoArte.io 销售智能合同代码在这里发布和验证](https://etherscan.io/address/0x3f18b2e57acd6f6c111936577529ac0dce7148b8#code)。这篇文章将它分解开来，这样你就可以用它作为建立你自己的网站的灵感。我们开始吧:

> **导入“open zeppelin-solidity/contracts/token/ERC 721/ERC 721 . sol”；
> 导入' open zeppelin-solidity/contracts/own able/own able . sol '；
> 导入' open zeppelin-solidity/contracts/life cycle/pausable . sol '；
> 导入' open zeppelin-solidity/contracts/life cycle/destrutable . sol '；**
> 
> /* *
> *[@ title](http://twitter.com/title)CryptoArteSales
> * CryptoArteSales——与[www . CryptoArte . io](http://www.cryptoarte.io)collection
> */
> contract**CryptoArteSales**可拥有，**可废弃**，**可毁坏** {

我们首先利用一些 [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-solidity) 所有权、生命周期和令牌合同:

*   **Ownable** :这将合同所有者设置为部署合同的地址，并为您提供一种稍后将所述所有权转移到您选择的不同地址的方法。它还为您提供了一个 **onlyOwner()** 修饰符，您可以使用它来确保函数只能由契约的所有者执行(我们稍后会用到)。
*   **可销毁:**这个生命周期构造提供了一种方法来销毁合同，并将任何剩余资金发送给所有者(或所有者选择的地址)。这在恐慌时期以及更换或升级整个合同时都很有用。此外，这听起来很酷，非常不可能完成的任务。
*   **Pausable:** 这个生命周期构造为您提供了一种 **pause()** 和 **unpause()** 操作的方法，这对于维护和其他目的非常有用。请注意，只有使用 **whenNotPaused()** 或 **whenPaused()** 修饰符的函数才会受到影响。

接下来，我们定义几个**事件**:

> 事件**发送**(地址索引收款人，uint256 金额，uint256 余额)；
> 事件**收到**(地址索引付款人，uint tokenId，uint256 金额，uint256 余额)；

我们的智能合同将发出:

*   **收到**事件时，它收到乙醚，以换取一个不可替代的令牌(例如:有人购买了一幅画)，和
*   **发送**事件时，资金从合同的以太网余额中提取(即:艺术家也要吃饭)。

接下来，我们定义了几个公共**变量**:

> **ERC 721**public**NFT address**；
> **uint256** 公共**当前价格**；

我们将使用 **nftAddress** 变量来保存不可替代代币合同的地址，并使用 **currentPrice** 变量来保存我们想要出售不可替代代币(在我们的例子中是绘画)的价格。既然我们已经介绍了这一点，理解构造函数应该非常简单:

> **建造师(address _nftAddress，uint 256 _ current price)public {**
> 要求(_nftAddress！&= address(0)&_ NFT address！=地址(this))；
> 要求(_ current price>0)；
> NFT address = ERC 721(_ NFT address)；
> current price = _ current price；
> }

构造器设置**初始** **nftAddress** 和 **currentPrice** 值，并且还执行一些基本的**验证**。

现在有趣的部分来了，不可替代代币的实际**销售**:

> /* *
> *[@ dev](http://twitter.com/dev)Purchase _ token ID
> *[@ param](http://twitter.com/param)_ token ID uint 256 token ID(绘画编号)
> */
> **函数 Purchase token(uint 256 _ token ID)public payable when not paused**{
> require(msg . sender！&= address(0)&msg . sender！=地址(this))；
> 要求(消息值> =当前价格)；
> require(NFT address . exists(_ token id))；
> 地址 token seller = NFT address . owner of(_ token id)；
> NFT address . safetransform(token seller，msg.sender，_ token id)；
> 发出收到(消息.发送者，_tokenId，消息.值，地址(this))。平衡)；
> }

**purchaseToken** 函数只有一个参数:不可替代令牌购买者想要获得的 **_tokenId** 。注意，我们在这里还使用了 ***应付款*** 关键字，它允许该方法从消息发送者(购买者)那里接收以太。还要注意，这里我们使用了 **whenNotPaused** 修饰符来确保在执行这个动作时契约没有被暂停。该函数首先执行一些验证:

*   它检查发送者不是零地址或销售合同本身，
*   它检查发送者是否至少发送了当前价格(但是如果他/她/它也选择了让购买者多付了钱)，
*   根据 ERC721 标准，它通过调用由不可替换令牌智能合约实现的 **exists** 方法来检查具有 _tokenId 的令牌是否确实存在。**注意:**这可能不需要，更像是一种防御性的编程方法/选择——下面的传输机制可能已经检查过了。

如果这些验证通过，则该函数通过调用不可替换令牌智能合约上的 **ownerOf** 方法(同样是 ERC721 标准的一部分)来获取令牌的当前所有者，然后调用**safetransforfrom**(也是在不可替换令牌智能合约上实现的，按照 ERC721)方法来将令牌实际发送给其买方，以换取收到的以太网。最后，它按照上面的方法发出**接收到的**事件。

> 提示:接受乙醚时一定要小心，因为你可能会再次发作。在这种情况下，我们将一个唯一的(不可替换的)令牌传输回来，所以我们可能是安全的，但是这种模式在其他情况下可能会有问题。

注意，**safetransforfrom**方法本身将执行许多其他验证，这些验证可能会导致该方法的整个执行恢复。其中一个验证与销售者(在我们的例子中是令牌的所有者)是否授权销售合同代表他们销售令牌有关。

遵循 ERC721 标准，您可以通过在不可替换的令牌智能合约上调用 **approve** (向一个特定令牌或给定的一组令牌授予权限，如果多次调用的话)或 **setApprovalForAll** (向所有者拥有的所有令牌授予权限)来实现这一点(在部署销售合约之后，您必须知道其地址)。

正如你可能已经注意到的，这里收到的资金将留在合同中，随着时间的推移增加它的余额。为了能真正使用收益，你需要一个从合同余额中提取资金的机制。输入 **sendTo** :

> /**
> * [@dev](http://twitter.com/dev) 发送/提取 _ 金额给 _ 收款人
> */ **函数 send to(address _ 收款人，uint 256 _ amount)public only owner {**
> require(_ 收款人！&=地址(0)&_ 收款人！=地址(this))；
> 要求(_ 金额>0&_ 金额< =地址(本)。平衡)；
> _ 收款人.转账(_ 金额)；
> 发出发送(_ 收款人，_ 金额，地址(此)。平衡)；
> }

这个方法相当简单:它验证一些东西，然后将所需的**_ 金额**转移给**_ 收款人，**然后发出**发送的**事件，如上所述。注意，这里我们使用 **onlyOwner** 修饰符来确保只有当前合同的所有者才能收回资金。

最后，我们还有一种方法来更新 currentPrice 变量:

> /* *
> *[@ dev](http://twitter.com/dev)Updates _ current price
> *[@ dev](http://twitter.com/dev)抛出 if _currentPrice 为零
> */
> **函数 setcurrent price(uint 256 _ current price)public only owner {** require(_ current price>0)；
> 当前价格= _ 当前价格；
> }

该方法验证新请求的价格是否大于零，然后更新将在下一次 purchaseToken 调用中使用的 currentPrice 变量。出于显而易见的原因，请注意再次使用了 **onlyOnwer** 修饰符。

> **提示:**这里需要注意的一个重要方面是，您可能会遇到 **setCurrentPrice** 和 **purchaseToken** 方法之间的竞争情况。如果您希望在更新价格时绝对确保没有人可以购买令牌，只需首先暂停合同，然后设置当前价格，然后再次取消暂停合同，等待每笔交易成功执行并确认后再执行下一笔交易。

## 为什么是多份合同？

虽然你可以在你的 NFT 合同中构建它(并在一个合同中包含所有内容)，但我个人喜欢在一个单独的合同中包含销售功能所带来的关注点的分离和灵活性(更容易的更新)。您甚至可以有多个销售合同，为不同的方实现不同的模型。

## 离别的思绪

这显然是一个非常简单的合同和模型，其中所有令牌在任何给定时间都以相同的价格提供。从这里开始，您可以尝试其他模型和想法，例如:

*   添加拍卖机制(起始价格、终止价格、时间范围等)
*   添加代销商支出(这样第三方可以因帮助你销售而获得佣金)
*   自动调整价格(例如，根据供应量)
*   成捆或成套出售代币
*   向回头客提供折扣
*   还有更多！

请自担风险使用(如果您发现任何问题，请告知我们)。

**如果你喜欢这个内容，请考虑鼓掌、** [**购买一幅隐雕画**](https://www.cryptoarte.io) **，或者两者都选:)**

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](/coinmonks/top-3-telegram-channels-for-crypto-traders-in-2021-8385f4411ff4) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [Godex.io 审核](/coinmonks/godex-io-review-7366086519fb) | [邀请审核](/coinmonks/invity-review-70f3030c0502) | [BitForex 审核](https://coincodecap.com/bitforex-review)
*   [10 本关于加密的最佳书籍](https://coincodecap.com/best-crypto-books) | [英国 5 个最佳加密机器人](https://coincodecap.com/uk-trading-bots)
*   [ko only 回顾](https://coincodecap.com/koinly-review) | [Binaryx 回顾](https://coincodecap.com/binaryx-review)|[Hodlnaut vs CakeDefi](https://coincodecap.com/hodlnaut-vs-cakedefi-vs-celsius)
*   [MoonXBT vs Bybit vs 币安](https://coincodecap.com/bybit-binance-moonxbt) | [硬件钱包](/coinmonks/hardware-wallets-dfa1211730c6)
*   [火币交易机器人](https://coincodecap.com/huobi-trading-bot) | [如何购买 ADA](https://coincodecap.com/buy-ada-cardano) | [Geco？一次审查](https://coincodecap.com/geco-one-review)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   [开发者最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [免费加密信号](/coinmonks/free-crypto-signals-48b25e61a8da) | [加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [杠杆代币的终极指南](/coinmonks/leveraged-token-3f5257808b22)