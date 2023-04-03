# 实施利害关系证明第 2 部分

> 原文：<https://medium.com/coinmonks/implementing-proof-of-stake-part-2-748156d5c85e?source=collection_archive---------0----------------------->

本文给出了一个在 node.js 中实现利益一致性算法的简单方法

![](img/67a3c3a562cf394e00d1dc1fd2774c83.png)

Source [bitcoin wiki](https://en.bitcoinwiki.org/upload/en/images/thumb/e/e5/Proof%E2%80%93of%E2%80%93Stake_%28PoS%29.jpg/500px-Proof%E2%80%93of%E2%80%93Stake_%28PoS%29.jpg)

在[上一篇](/coinmonks/implementing-proof-of-stake-e26fa5fb8716)文章中，我们介绍了 stake 的证明，它是如何工作的，还讨论了我们将要实现的代码的设计和架构。

在这篇文章中，我们将开始编码。

# 先决条件:

1.  [Node.js](https://nodejs.org/en/download/) 和 [NPM](https://www.npmjs.com/)
2.  [文本编辑器](https://code.visualstudio.com/download)(带有深色主题😛)
3.  [Postman](https://www.getpostman.com/) 或任何其他 app 与 HTTP APIs 交互。

***就是这样。***

# 让我们编码:

我们将从创建项目的根目录开始。为您的项目命名。就说 POSchain 吧。

```
mkdir poschain
cd poschain
```

创建新的节点项目

```
npm init -y
```

我们需要一个名为 nodemon 的包来运行我们的开发服务器，并在每次保存时重新加载服务器。这个工具让我们免去了很多麻烦。

```
npm install nodemon --save-dev
```

好吧。继续前进。

# 街区

区块链是由块组成的，所以让我们创建一个块类。块具有以下主要属性:

时间戳—块的创建时间，以毫秒为单位

lastHash —链上最后一个块的哈希

哈希-当前块的哈希

数据—块或事务中的数据

验证者——制作这个程序块的人的地址

签名—由验证程序签名的块的加密哈希

因此，在根目录下创建一个新文件`block.js`,并创建一个具有这些属性的类。

the block

另外，添加一个`toString()`函数，该函数将以可读格式打印块的细节。

由于在链的开始没有积木，区块链有了创世纪积木的概念。创世街区服务于成为区块链起源的目的。这是一个硬编码的块，包含时间戳、lastHash、Hash 和数据值的虚拟值。

让我们用一些硬编码值给我们的块类添加一个静态的`genesis()`函数。该函数将创建一个带有虚拟值的新块。这是静态的，因为我们不想创建一个实例来调用这个函数。

接下来我们要添加到 block 类中的是基于数据和最后一个散列创建新块的能力。姑且称之为`createBlock()`

这个静态的`createBlock()`函数目前有两个参数，最后一个块和数据，基于这两个参数它生成一个新的块。我们在整个项目中的许多功能将是静态的。

为了生成哈希值，我们需要一个名为`crypto-js`的节点包，让我们安装它。

```
npm i crypto-js --save
```

从此模块导入 sha-256 函数。

```
const SHA256 = require('crypto-js/sha256');
```

我们不会直接使用散列函数，相反，我们将创建一个单独的函数，因为我们将来会再次使用它。

The backticks and dollar syntax, that’s ES6.

让我们用上面的东西创建一个`createBlock()`函数。

在接下来的教程中，我们将在实现钱包时添加验证器和签名。

# 区块链

在根目录下创建 `blockchain.js`文件。要创建区块链类，我们需要导入块类。

在构造函数中，我们将为链提供一个初始块。在这里，我们将利用我们创建的静态成因函数来启动我们的链。

The blockchain class

接下来，让我们给我们的区块链类通过创建一个`addBlock(data)`函数向链中添加更多块的能力。

在这里，我们将使用我们的`createBlock()`功能来创建一个新的块，然后将其推送到链中。我们将访问链上的最后一个块，然后使用传递给`addBLock()`的数据调用`createBlock()`函数

到目前为止，我们的 blockchain.js 文件是这样的。

酷。

接下来，我们将向区块链添加更多功能，以支持多个贡献者。

*感谢您的阅读。* ***在下一部分，我们将编写代码来验证区块链，并增加对多个节点的支持。*** *希望您喜欢编码。* ***如果你发现这有帮助，就鼓掌欢呼。***

*如果你对区块链、以太或整个世界有任何疑问，请留言。:)*

> [直接在您的收件箱中获取最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)