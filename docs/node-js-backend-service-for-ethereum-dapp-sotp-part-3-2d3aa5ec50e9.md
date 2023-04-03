# 节点。以太坊 Dapp 的 JS 后端服务— SoTP 第 3 部分

> 原文：<https://medium.com/coinmonks/node-js-backend-service-for-ethereum-dapp-sotp-part-3-2d3aa5ec50e9?source=collection_archive---------0----------------------->

![](img/eb986f6d52f2c6c47a61751d6cd8fe43.png)

Photo by [Thomas Lefebvre](https://unsplash.com/photos/gp8BLyaTaA0?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

当我们谈论 [**中的**Dapp**part 1**](/coinmonks/learn-solidity-shark-of-the-pool-part-1-ac0f733eecdd)和 [**Part2**](/coinmonks/testing-solidity-dapp-sotp-part-2-9685b3375aaf) 时，人们可能会认为我们可以摆脱“蹩脚的旧”**后端服务**和集中式软件。嗯，事实并非完全如此。区块链在安全传输价值、保护应用程序的关键逻辑甚至用户身份验证方面的工作令人印象深刻。但它仍然有点慢，更糟糕的是，[作为数据存储贵得离谱](https://hackernoon.com/ether-purchase-power-df40a38c5a2f)。

**Dapp**的演示可在[这个地址](http://34.210.217.34/)获得。

所有的**源代码**都可以在[这个 GitHub 库](https://github.com/joze144/pool-shark-api)中免费获得。

加入我们: [Solidity 开发社区，了解更多信息](https://solidity_devs.select.id/bjxx)

> [发现并回顾最佳区块链 api 和节点产品](https://coincodecap.com/category/blockchain-node-and-api)

# 后端服务的目标

我们想设计一个后端，解决区块链的缺点。我们希望通过我们的服务实现的要点:

*   从区块链获取数据，并将其存储到易于访问的数据库中。
*   执行数据分析和数据操作。
*   专为从区块链恢复数据而设计。
*   基于事件的设计，它应该与多个区块链数据输入(RPC 和 WebSocket)一起工作。
*   交易可能会因为一个[叔叔阻塞](https://www.mycryptopedia.com/orphan-uncle-genesis-blocks-explained/)而被拒绝或删除。服务必须足够强大才能处理这种情况。

区块链将是数据生成器和决策者。它直接处理用户进行的交易，并存储历史交易数据。后端服务将解析区块链创建的数据，并将其呈现给网络。这样我们可以提供更快更好的用户体验。

# 后端实现

对于后端服务，我使用的是[节点。JS](https://nodejs.org) 带 [MongoDB](https://www.mongodb.com/) 和 [Express 框架](https://expressjs.com/)。解析数据是通过 [web3 库](https://web3js.readthedocs.io/)完成的。我使用的是 Infura RPC provider ，这样我就不必运行自己的以太坊节点。一个缺点是我没有 WebSocket，我只使用 RPC。

可靠性合同被部署到 [Ropsten 区块链。地址 0x 70 e 5044 ce 689132 D8 ECF 6 ee 3433 af 796 f8e 46575](https://ropsten.etherscan.io/address/0x70e5044cE689132d8ECf6EE3433AF796F8E46575)

## 区块链数据存储

在区块链上存储数据有两种方式:

*   **合同存储**可从合同内访问。这是变量，状态的每一个变化都要消耗气体，很多气体。32 字节是一个存储单位。通常需要花费 20.000 汽油来改变价值。如果之前已经设置了存储并且正在修改，则更少。使用[https://ethgasstation.info/](https://ethgasstation.info/)查看当前天然气价格。
*   **日志事件**是从事务内部触发(发出)的，是永久的、不可逆的存储。它们不能从契约中访问，但是我们可以通过查询以太坊节点用我们的后端服务访问它们。它们是便宜得多的存储，每日志花费 375 gas 加上日志中每字节 8 gas。

在**合同存储**中，您将存储数据的最新状态，这对于合同正确处理新交易至关重要。其余的数据，在合同中不需要，但是 Dapp 工作需要，应该通过**日志事件**发出。

我们已经在智能合约中定义了**日志事件**。

```
/// @notice Event propagated on each pool creation **event** LogPoolCreated(**address** indexed _pool, **string** _name, **uint256** _rate, **uint256** _deadline);*/// @notice Event propagated on every executed transaction* **event** LogTransfer(**address** indexed _from, **address** indexed _to, **uint256** _value);

*/// @notice Event propagated on new deposit to the pool* **event** LogIssue(**address** indexed _member, **uint256** _value);

*/// @notice Event propagated on new address has the most tokens* **event** LogNewShark(**address** indexed _shark, **uint256** _value);
```

我们将能够解析每个创建的池、每个到池的存款、每个转移和每个 shark 更改的数据。

## 解析事件

您可以使用 [Etherscan](https://etherscan.io/) 来查看从契约中发出的事件。这是我的一份测试合同。我们看到它是有效的，因为每个事务事件都被发出。但是我们如何在后端解析它们呢？

```
**const contractInstance** = **new** web3.**eth**.**Contract**(contract.abi, contractAddress)**const** events = **await contractInstance**.getPastEvents(**'allEvents'**, {**fromBlock**: **fromBlock**})
**const** promises = _.map(events, (obj) => {
  **return** *handleEvent*(obj)
})

**await** Promise.all(promises)
```

这是我的一个解析器使用 [**web3 库**](http://web3js.readthedocs.io/en/1.0/) 得到的代码。首先，您需要一个合同实例，指定合同 **ABI** 和区块链的地址。合同 **ABI** 应在 JSON 文件中，由块菌生成。如果部署了块菌，地址也会返回到控制台中。

我的后端是每 30 秒查询一次 Ethereum 节点，查找每一个有事件的合同(希望没有太多的请求)。不幸的是，对于 **RPC** 节点，我还没有找到更好的解决方案。 **WebSocket** 正在实时发送事件，但是 **Infura** 还没有提供，在我的服务器上运行**以太网节点**也不太方便。您可以通过运行 [geth node](https://github.com/ethereum/go-ethereum/wiki/geth) 、[奇偶校验 node](https://www.parity.io/) 或 [Mist node](https://github.com/ethereum/mist/releases) 来实现。

## 为失败而设计

这是对其中一个日志事件的响应。

```
{
  "address": "0xfc4F758BbB89F3570b7D2C2645922C9AEF0C5d2d",
  "blockNumber": 3135323,
  "transactionHash": "0x3d4f41e2fedecaa7d424b183ef367fc312f58f41dbac2ad92f654b594774d1fa",
  "transactionIndex": 19,
  "blockHash": "0x56dc21ea05f4dbc932cc786ec16c29da63f843d6ea8db4271fd8e224f34e5f66",
  "logIndex": 8,
  "removed": false,
  "id": "log_cb1c6ac7",
  "returnValues": {
    "0": "0xaD5f3827284e60fbdA8836266919d4c376fca352",
    "1": "0xb3D9B300bFaeafaE61f97C8b375E4Cc72c2Cabc3",
    "2": "100000000000000000",
    "_from": "0xaD5f3827284e60fbdA8836266919d4c376fca352",
    "_to": "0xb3D9B300bFaeafaE61f97C8b375E4Cc72c2Cabc3",
    "_value": "100000000000000000"
  },
  "event": "LogTransfer",
  "signature": "0x0a85107a334eae0d22d21cdf13af0f8e8125039ec60baaa843d2c4c5b0680174"
  }
}
```

您可以[查看文档](http://web3js.readthedocs.io/en/1.0/web3-eth-contract.html#id36)了解每个字段的确切含义。在这一点上，我想概述的是，接受事件并不意味着它是最终的。它可能还没有包含在块中，所以**块哈希**字段为**空**。即使是这样，也可能因为某个 [**叔叔阻止了**](https://www.mycryptopedia.com/orphan-uncle-genesis-blocks-explained/) 而被移除。在这种情况下，被**拒绝的字段**将变为**真**。

交易所通常使用 12 个或更多块确认，以确保交易不可逆。更多信息请阅读[维塔利克的帖子](https://blog.ethereum.org/2015/09/14/on-slow-and-fast-block-times/)。即使对于已经被解析的事件，您也能够更新数据库中的条目，这一点很重要。并检测至少最近 12 个街区内的被拒绝事件。

## 后端应用编程接口

通过 **Express REST API** 向最终用户提供数据。您可以在 **router.js** 文件中看到路由。

```
**const** express = require(**'express'**)

*// Middleware* **const** pagination = require(**'./pagination'**)

*// Controllers* **const poolController** = require(**'./pool/controller'**)
**const fishTokenController** = require(**'./fishToken/controller'**)

**const** apiRoutes = express.Router()
**const** v1Routes = express.Router()

*// Set v1 routes as subgroup/middleware to apiRoutes* apiRoutes.use(**'/v1'**, v1Routes)

v1Routes.post(**'/pool/list'**, pagination, **poolController**.*getPools*)
v1Routes.get(**'/pool/:id'**, **poolController**.*getPoolById*)
v1Routes.get(**'/token/:id'**, **fishTokenController**.*getTokenById*)

module.exports = apiRoutes
```

目前只有几条路线。一旦我知道了我需要什么让**反应网络应用**发挥作用，我会在后面补充更多。

# 结论

在这一部分中，我们创建了一个**后端服务**，它存储从区块链发出的事件，并通过 **REST API** 将它们提供给最终用户。直接在区块链上做这些是非常不切实际的，而且非常昂贵。这就是为什么我们需要一个后端服务来支持我们的 **Dapp** 。

考虑只提供所有可用池的列表。我们应该获取所有发出的 **LogPoolCreated** 事件，并解析客户端应用程序上的数据。为此创建一个搜索过滤器或者只为截止日期尚未到期的**池**服务怎么样？

在后端服务上，我们可以做到这一切，因为存储数据很便宜，操作起来也更容易。

**Dapp**的演示可在[这个地址](http://34.210.217.34/)获得。

所有的**源代码**都可以在[这个 GitHub 库](https://github.com/joze144/pool-shark-api)中免费获得。

## 该系列的部分内容:

*   [第 1 部分——可靠性智能合同](/@jozhe/learn-solidity-shark-of-the-pool-part-1-ac0f733eecdd)
*   [第 2 部分——测试坚固性 Dapp](/@jozhe/testing-solidity-dapp-sotp-part-2-9685b3375aaf)
*   **第三部分——节点。Solidity Dapp 的 JS 后端服务**
*   [第 4 部分—使用 MetaMask Web3 对 Web 应用程序进行反应](/@jozhe/react-web-dapp-with-metamask-web3-sotp-part-4-f252ebe8d07f)

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [币安交易机器人](/coinmonks/binance-trading-bots-d0d57bb62c4c) | [OKEx 审查](/coinmonks/okex-review-6b369304110f) | [阿塔尼审查](https://coincodecap.com/atani-review)
*   [最佳加密交易信号电报](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b) | [MoonXBT 评论](/coinmonks/moonxbt-review-6e4ab26d037)
*   [如何在 Bitbns 上购买柴犬(SHIB)币？](https://coincodecap.com/buy-shiba-bitbns) | [购买 Floki](https://coincodecap.com/buy-floki-inu-token)
*   [CoinFLEX 评论](https://coincodecap.com/coinflex-review) | [AEX 交易所评论](https://coincodecap.com/aex-exchange-review) | [UPbit 评论](https://coincodecap.com/upbit-review)
*   [十大最佳加密货币博客](https://coincodecap.com/best-cryptocurrency-blogs) | [YouHodler 评论](https://coincodecap.com/youhodler-review)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   开发人员的最佳加密 API
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [免费加密信号](/coinmonks/free-crypto-signals-48b25e61a8da) | [加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [杠杆代币的终极指南](/coinmonks/leveraged-token-3f5257808b22)