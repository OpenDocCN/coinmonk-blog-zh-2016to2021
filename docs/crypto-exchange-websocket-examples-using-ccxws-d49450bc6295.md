# 使用 CCXWS 的加密交换 Websocket 示例

> 原文：<https://medium.com/coinmonks/crypto-exchange-websocket-examples-using-ccxws-d49450bc6295?source=collection_archive---------0----------------------->

![](img/f431a076a03ee3118c48b6a2e9429ea1.png)

> 连接到各大交易所的教程。

开发依赖于加密货币交易数据的应用程序非常复杂。每一个交换都提供了不同的 API，这些 API 都是独一无二的，令人困惑。

这就是为什么我们制作了一系列教程，帮助像您这样的开发人员轻松开始管理 exchanges。

[](https://blog.shrimpy.io/blog/ccxt-live-crypto-exchange-price-ticker) [## CCXT 实时加密交易所报价器[示例教程] | Shrimpy

### 我们将介绍两种不同的方法来生成实时价格行情。第一个策略是访问数据…

blog.shrimpy.io](https://blog.shrimpy.io/blog/ccxt-live-crypto-exchange-price-ticker) [](https://blog.shrimpy.io/blog/ccxt-crypto-exchange-order-book-snapshot) [## CCXT 加密交换订单簿数据[示例教程] | Shrimpy

### 订单簿指定了交易所中当前开放的所有可用订单。每个订单代表一个…

blog.shrimpy.io](https://blog.shrimpy.io/blog/ccxt-crypto-exchange-order-book-snapshot) 

加密货币交易商和开发人员一直在寻找最快的方式来访问加密交易所交易和订单数据，以便他们能够更快地做出决策，并在机会出现时更好地捕捉机会。

为了提供这种速度，许多开发人员转向 websocket APIs。websocket 流提供最新的价格信息，因为它在交易所发生变化。在不要求开发者不断轮询新数据的情况下，只要有变化，交易所就会立即发送最新的交易或订单数据。

这是从交易所获取市场数据的最快方式。订阅每个交易对通常是一次性事件。在此之后，交易所将自动发送更新的交易或订单数据，以便您可以就如何以及何时执行交易做出实时决策。

在本文中，我们将提供多个示例，说明如何通过 websockets 为每个主要的加密交换访问 live exchange 数据。

本教程中的示例将涵盖如何使用 [**CCXWS JavaScript 库**](https://www.npmjs.com/package/ccxws) 和[**Shrimpy Node.js 库**](https://www.npmjs.com/package/shrimpy-node) 。

# CCXWS

[CCXWS](https://www.npmjs.com/package/ccxws) 是一个开源库，由带给你 [CCXT](https://github.com/ccxt/ccxt) 的同一个开发者提供。

## **安装**

在编写任何代码之前，我们需要使用 npm 安装 CCXWS 包。

```
npm install ccxws
```

## **示例**

以下示例将连接到两个不同的 websocket 流。一个来自币安的交易流和一个来自 KuCoin 的二级订单流。

```
const ccxws = require("ccxws");const binance = new ccxws.binance();
const kucoin = new ccxws.kucoin();// select binance markets
const subscribeData1 = {
  id: "ETHUSDT",
  base: "ETH",
  quote: "USDT",
};// select kucoin markets
const subscribeData2 = {
  id: "LTCBTC",
  base: "LTC",
  quote: "BTC",
};// handle the trade and order book events from websocket
binance.on("trade", trade => console.log(trade));kucoin.on("l2snapshot", snapshot => console.log(snapshot));// subscribe to the trade and order book events
binance.subscribeTrades(subscribeData1);kucoin.subscribeLevel2Snapshots(subscribeData2);
```

在连接到这些 websocket 流之后，您将立即开始接收更新。每次更新都必须使用定制逻辑来处理、组织和存储数据。

# 多虾的

在开始我们的 Node.js 示例之前，先注册一个 [**Shrimpy 通用加密交易 API**](https://developers.shrimpy.io/)账户。

注册后，选择“创建 API 密钥”按钮来生成您的主 Api 密钥。在生成您的密钥并将它们存储在一个安全的位置之后，确保您已经在 API 密钥上启用了“**数据**”权限。

> 注意:在开始访问 websocket 数据之前，您需要选择一个订阅计划。对于本教程，我们建议订阅“个人”计划，该计划将为您提供对我们的 websockets 以及交易、投资组合和交易所账户管理端点的几乎无限制的访问。

```
npm install shrimpy-node
```

## **例子**

类似于我们上一个 CCXT 的例子，这个例子将订阅两个不同的 websocket 流。一个 websocket 流用于比特币基地专业贸易频道，第二个订阅用于北海巨妖订单簿频道。

```
import { ShrimpyApiClient, ShrimpyWsClient, ISubscriptionRequest, IWebsocketMessage, IErrorMessage } from 'shrimpy-node';// handle errors from the websockets
let errorHandler = (error: IErrorMessage) => { console.log(error) };// create the Shrimpy API client using Shrimpy master keys
const publicKey = 'your_public_key';
const privateKey = 'your_private_key';
let shrimpyApiClient = new ShrimpyApiClient(publicKey, privateKey);// create the Shrimpy websocket client using a token
let token = shrimpyApiClient.getToken();
let shrimpyWsclient = new ShrimpyWsClient(errorHandler, token);// declare the markets for subscribing and unsubscribing
const subscribeData1: ISubscriptionRequest = {
    "type": "subscribe",
    "pair": "btc-usd",
    "exchange": "coinbasepro",
    "channel": "trade"
};const subscribeData2: ISubscriptionRequest = {
    "type": "subscribe",
    "pair": "eth-btc",
    "exchange": "kraken",
    "channel": "orderbook"
};const unsubscribeData1: ISubscriptionRequest = {
    "type": "unsubscribe",
    "pair": "btc-usd",
    "exchange": "coinbasepro",
    "channel": "trade"
};const unsubscribeData2: ISubscriptionRequest = {
    "type": "subscribe",
    "pair": "eth-btc",
    "exchange": "kraken",
    "channel": "orderbook"
};let handler = (msg: IWebsocketMessage) => { console.log(msg); };// connect and subscribe to the websocket channels
client.connect();client.subscribe(subscribeData1, handler);
client.subscribe(subscribeData2, handler);client.unsubscribe(unsubscribeData1);
client.unsubscribe(unsubscribeData2);client.forceDisconnect();
```

> 注意:这些例子没有解释如何在接收每个更新时作为客户机本地维护订单簿 websocket。将需要额外的工作来维护订单簿的状态。这将需要根据您对订单簿的要求进行开发。

Shrimpy 除了 Node.js 还支持一个 Python 客户端，你可以在这里 找到那个客户端 [**。**](https://github.com/shrimpy-dev/shrimpy-python)

# 结论

CCXWS 提供了一种便捷的方式来为每个单独的交换订阅 websockets。然而，CCXWS 并没有跨交换聚合 websocket 流，以便通过 API 进行通用访问。

维护每个 websocket 可能是一项重要的工作，因此[**Shrimpy web socket API**](https://developers.shrimpy.io/)旨在简化您管理所有 exchange websocket 连接的方式。

今天试用一下 [**Shrimpy Trading &数据 API**](https://developers.shrimpy.io/)。这是开始在各大交易所收集数据的最简单方法。收集**历史市场数据**，访问**实时 websockets** ，执行**高级交易策略**，**管理无限数量的用户**。

*   [Shrimpy Python 库](https://github.com/shrimpy-dev/shrimpy-python)
*   [Shrimpy Node.js 库](https://github.com/shrimpy-dev/shrimpy-node)

不要忘记在 [Twitter](https://twitter.com/ShrimpyApp) 和[脸书](https://www.facebook.com/ShrimpyApp)上关注我们的更新，并在 [Telegram](https://t.me/ShrimpyGroup) 上向我们令人惊叹的活跃社区提出任何问题。

*虾队*

# 附加阅读

[](https://blog.shrimpy.io/blog/ccxt-live-crypto-exchange-price-ticker) [## CCXT 实时加密交易所报价器[示例教程] | Shrimpy

### 我们将介绍两种不同的方法来生成实时价格行情。第一个策略是访问数据…

blog.shrimpy.io](https://blog.shrimpy.io/blog/ccxt-live-crypto-exchange-price-ticker) [](https://blog.shrimpy.io/blog/shrimpy-vs-ccxt-centralization-in-a-decentralized-ecosystem) [## 与 CCXT:分散生态系统中的集中化案例

### 加密货币领域有一种让一切开放、去中心化和免费的驱动力。当谈到…

blog.shrimpy.io](https://blog.shrimpy.io/blog/shrimpy-vs-ccxt-centralization-in-a-decentralized-ecosystem) [](https://hackernoon.com/trading-crypto-through-apis-a84b23cd05b) [## 通过 API 交易加密货币

### 在本文中，我们将向您展示如何使用 API 设置加密货币交易。

hackernoon.com](https://hackernoon.com/trading-crypto-through-apis-a84b23cd05b) [](https://blog.shrimpy.io/blog/historical-crypto-price-candlestick-ohlcv-data) [## 历史加密价格烛台(OHLCV)数据| Shrimpy

### 为了扩展我们的历史数据产品，Shrimpy 团队在 Shrimpy developer 中添加了新的端点…

blog.shrimpy.io](https://blog.shrimpy.io/blog/historical-crypto-price-candlestick-ohlcv-data) 

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com?utm_source=coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://coincodecap.com?utm_source=coinmonks)