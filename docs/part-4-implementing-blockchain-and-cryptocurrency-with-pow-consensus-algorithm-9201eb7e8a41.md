# 第 4 部分:用 PoW 共识算法实现区块链和加密货币

> 原文：<https://medium.com/coinmonks/part-4-implementing-blockchain-and-cryptocurrency-with-pow-consensus-algorithm-9201eb7e8a41?source=collection_archive---------1----------------------->

在 node.js 中使用工作共识算法证明，小规模、易于理解、全面、逐步实施区块链和加密货币

![](img/ca1a2e2c4224dbbeb2b00eaca6e45a01.png)

Source: [Bitcoin Wiki](https://en.bitcoinwiki.org/wiki/Proof-of-work)

在[之前的文章](/coinmonks/part-3-implementing-blockchain-and-cryptocurrency-with-pow-consensus-algorithm-d9b8cb928e3e)中，我们为我们的区块链添加了多个贡献者的能力，并在其上创建了一个 HTTP API。

现在我们可以与区块链互动，但我们仍然只有一个用户。为了拥有多个对等点，我们将制作一个 p2p 服务器，并使用 WebSockets 连接这些对等点。每个对等体与其他对等体通信并传输数据。

当我们启动我们的应用程序时，我们将连接到一些同行，当我们启动我们的应用程序时，我们将通过他们的地址。

# P2P 服务器

我们需要一个 p2p 服务器，以便分散网络中的不同对等点可以通过网络相互发送块、链、事务和其他消息。

为了在 node.js 中实现 p2p 服务器，我们需要一个名为`ws`的模块，这是一个轻量级的、易于使用的网络套接字模块。还有各种其他模块可用，但为了简单起见，我们将坚持使用这个模块。

```
npm i ws --save
```

在 app 内，目录下创建一个文件`p2p-server.js`。导入`ws`模块，为服务器定义一个`P2P_PORT`。像我们的 HTTP 服务器一样，我们可以使用环境变量从终端分配一个端口。

```
const P2P_PORT = process.env.P2P_PORT || 5001;
```

我们需要一个`peers` 列表，当我们启动应用程序时，我们将连接到它。每个对等体都有一个这样的地址

```
PEERS = ws://localhost:5002 P2P_PORT=5001 HTTP_PORT=3001 npm run devconst peers = process.env.PEERS ? process.env.PEERS.split(',') : [];
```

因此，我们可以将我们希望从终端连接到的对等点地址作为一个环境变量进行传递，并在以后访问我们的 p2p 服务器。

现在让我们在 p2p-server.js 中创建一个 P2pServer 类，它将保存所有的消息处理程序和事件侦听器。

p2p 服务器将有一个区块链变量，这将是我们在应用程序中使用的相同的区块链。因此，我们将传递一个区块链实例作为依赖项。我们的 p2p 服务器也有一个在给定时间内连接的套接字列表。

我们使用`peers`列表通过遍历每个对等体并将其套接字连接对象保存在套接字列表中以备后用来连接对等体。

让我们实施我们到目前为止所讨论的。

P2pserver class

在我们的`app/index.js`文件中，我们将创建 p2pserver 的实例，传递区块链实例，然后启动 p2pserver。

```
const P2pServer = require('./p2p-server.js');const p2pserver = new P2pServer(blockchain);
// passing blockchain as a dependency.
.
.
.p2pserver.listen(); // starts the p2pserver
```

好了，现在让我们测试我们的 p2p 服务器。

启动终端，打开三个标签。在第一次选项卡运行中

```
npm run dev
```

在第二个

```
HTTP_PORT = 3002 P2P_PORT = 5002 PEERS = ws://localhost:5001 npm run dev
```

在第三个

```
HTTP_PORT = 3003 P2P_PORT = 5003 PEERS = ws://localhost:5002,ws://localhost:5001 npm run dev
```

在第一个终端中，我们将得到输出

```
Listening for peer to peer connection on port : 5001
listening on port 3001
Socket Connected
Socket Connected
```

在第二个终端中，我们将得到输出

```
Listening for peer to peer connection on port : 5002
listening on port 3002
Socket Connected
Socket Connected
```

在第一个终端中，我们将得到输出

```
Listening for peer to peer connection on port : 5003
listening on port 3003
Socket Connected
Socket Connected
```

我们的插座现在互相连接了。

厉害！让我们使用这个 P2P 服务器来同步区块链，以构建一个完全分散的区块链网络。

让我们给 p2p 服务器添加一个消息处理程序。这个 messageHandler 函数将接受一个套接字作为参数。

I handle your messages

我们需要将这个处理程序分配给每个套接字。每当我们在`connectSocket()`函数中建立一个新的连接时，我们都可以这样做。

当我们连接到对等体时，我们需要向它发送消息。因此，我们可以在连接上使用`socket.send()`来这样做，我们希望将我们的链发送给对等体。因此，当我们连接到一个套接字时，我们将把链作为消息发送给它们。

*注意:理想情况下，我们会发送块，因为链的大小非常大。但是为了简单起见，我们将发送整个链。*

```
connectSocket(socket){// push the socket too the socket array
        this.sockets.push(socket);
        console.log("Socket connected");// register a message event listener to the socket
        this.messageHandler(socket);// on new connection send the blockchain chain to the peersocket.send(JSON.stringify(this.blockchain));
}
```

如果您运行两个不同的应用程序，您会看到另一个对等体的链打印在第一个对等体的控制台上，反之亦然。这意味着我们的套接字正在正确地相互发送消息。

为了使我们的链与对等体的链同步，我们可以检查接收到的链是否有效和更长。我们已经在区块链类中实现了这个功能。让我们在 p2pserver 类中使用`replaceChain()`函数，当我们从一个对等点接收到一个链时。

在`messageHandler(socket)`功能中添加

```
this.blockchain.replaceChain(data);
```

为了让我们的生活更容易，我们将在我们的 p2pserver 中添加两个助手函数`sendChain()`和`syncChain()`。

`sendChain(socket)`将用于发送我们的链到一个套接字

The chain sender

`syncChain(socket)`将在我们的索引文件中用来同步链

The chain synchronizer

现在，在我们的 app/index.js 文件中，每当我们向链中添加一个新块时，我们都可以使用`syncChain()`函数。

```
app.post('mine',(req,res) => {
    .
    .
    .
    p2pserver.syncChain();
}
```

我们为 p2pserver 编写的最终代码将类似于

通过启动两个应用程序实例来测试这个功能。在第一个应用程序中添加新块。

现在打开第二个终端，您将看到控制台中打印的输出。

```
Listening for peer to peer connection on port : 5002
listening on port 3002
Socket Connected
Replacing the current chain with new chain
```

您可以通过使用第二个应用程序的 get 端点来确认这一点。您可以对第二个实例进行同样的尝试，第一个实例的链将被更长的更改所取代。

太好了！我们的区块链开始同步了。

我们已经创建了一个去中心化的网络，在这个网络中，对等体可以相互通信，并发送他们自己版本的区块链。基于链的长度，对等体可以选择忽略或替换接收到的链。

在下一篇文章中，我们将改进我们的工作证明算法，并增加随机数和难度的特性。

[第五部分:用 PoW 共识算法实现区块链和加密货币](/coinmonks/part-5-implementing-blockchain-and-cryptocurrency-with-pow-consensus-algorithm-a7f8853d23dc)

*感谢您的阅读。* ***在下一部分中，我们将扩展我们的工作证明算法，并将随机数和难度的概念添加到我们的项目中。*** *希望你喜欢编码。如果你发现这很有帮助，请鼓掌。*

如果您对区块链、以太坊或整个世界有任何疑问，请发表评论。:)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)