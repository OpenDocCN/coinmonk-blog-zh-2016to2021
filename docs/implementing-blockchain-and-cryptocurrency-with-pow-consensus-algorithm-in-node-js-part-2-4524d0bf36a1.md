# 第 2 部分:用 PoW 共识算法实现区块链和加密货币

> 原文：<https://medium.com/coinmonks/implementing-blockchain-and-cryptocurrency-with-pow-consensus-algorithm-in-node-js-part-2-4524d0bf36a1?source=collection_archive---------2----------------------->

在 node.js 中使用工作共识算法证明，小规模、易于理解、全面、逐步实施区块链和加密货币

![](img/ca1a2e2c4224dbbeb2b00eaca6e45a01.png)

Source: [Bitcoin Wiki](https://en.bitcoinwiki.org/wiki/Proof-of-work)

在[的上一篇文章](/coinmonks/implementing-blockchain-and-cryptocurrency-with-pow-consensus-algorithm-part-1-545fb32be0c2)中，我们介绍了区块链和工作证明，还讨论了我们将要实现的代码的设计和架构。

在这篇文章中，我们将开始动手。

# 先决条件:

1.  [Node.js](https://nodejs.org/en/download/) 和 [NPM](https://www.npmjs.com/)
2.  [文本编辑器](https://code.visualstudio.com/download)(带有深色主题😛)
3.  [Postman](https://www.getpostman.com/) 或任何其他 app 与 HTTP APIs 交互。

***就是这样。***

# 让我们编码:

我们将从创建项目的根目录开始。为您的项目命名。假设是 Nodechain。

```
mkdir nodechain
cd nodechain
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

区块链是由块组成的，所以让我们创建一个块类。块有 4 个主要属性:

时间戳—块的创建时间，以毫秒为单位

lastHash —链上最后一个块的哈希

哈希-当前块的哈希

数据—块或事务中的数据

因此，在根目录下创建一个新文件`block.js`,并创建一个具有这些属性的类。

The block class

同样，让我们添加一个`toString()`函数，它将以可读的格式打印块的细节。

A readable printing function

在区块链中，每个数据块的哈希值是根据之前数据块的哈希值计算的。

由于在链的开始没有积木，区块链有了创世纪积木的概念。创世街区服务于成为区块链起源的目的。这是一个硬编码的块，包含时间戳、lastHash、Hash 和数据值的虚拟值。

让我们用一些硬编码值给我们的块类添加一个静态的`genesis()`函数。该函数将创建一个带有虚拟值的新块。这是静态的，因为我们不想创建一个实例来调用这个函数。

What came first egg? Chicken? No the genesis block.

接下来我们要添加到 block 类中的是基于数据和最后一个散列创建新块的能力。姑且称之为`mineBlock()`

这个静态的`mineBlock()`函数接受两个参数，最后一个块和数据，并基于这两项生成一个新的块。我们在整个项目中的许多功能将是静态的。

为了生成哈希值，我们需要名为`crypto-js`的节点包，让我们安装它。

```
npm i crypto-js --save
```

从该模块导入 sha-256 函数。

```
const SHA256 = require('crypto-js/sha256');
```

我们不会直接使用散列函数，相反，我们将创建一个单独的函数，因为我们将来还会用到它。

The backticks and dollar syntax, that’s ES6.

让我们用上面所有的东西创建一个`mineBlock()`函数。

我们将很快在这里添加更多的功能。

这是我们到目前为止创建的块类。

The Block

现在我们已经创建了块类，我们必须测试它。为了测试这个类，我们最好创建一个测试环境。对于这个项目，我们将使用 jest。

```
npm i jest --save-dev
```

*注意:如果你在安装 jest 时遇到问题，我曾经有过，试着降级到一个较低的版本，它对我很有效。*

使用 jest，我们可以在 javascript 项目中执行测试文件。为了找到测试文件，jest 将查找扩展名为*.test.js 的文件，因此为了测试我们的 block 类，让我们创建一个 block.test.js 文件。

让我们创建一些测试用例。我们将测试构造函数、genesis、hash 和 mineBlock 是否按预期工作。

Sweet tests

要运行该文件，请对 package.json 文件中的脚本进行一些更改。在其中添加一个测试脚本。

```
"test": "jest --watchAll"
```

我们来做测试。

```
npm run test
```

我们将通过两次测试，表明我们的功能运行良好。

检查测试是否正常。对测试文件进行一些更改，然后再次运行测试。你会发现测试会失败。

酷毙了。我们已经创建了一个基本块。让我们继续创建一个区块链类来链接这些块。

# 区块链

在根目录下创建一个 `blockchain.js`文件。要创建区块链类，我们需要导入 block 类。

在构造函数中，我们会给链一个初始块。这里，我们将利用我们创建的静态 genesis 函数来开始我们的链。

The blockchain class

接下来，让我们通过创建一个`addBlock(data)`函数，给我们的区块链类添加更多块的能力。

这里，我们将使用我们的`mineBlock()`函数创建一个新的块，然后将它推送到链中。我们将访问链上的最后一个块，然后使用传递给`addBLock()`的数据调用`mineBlock()`函数

adding new block to the chain

这是我们的 blockchain.js 文件到目前为止的样子。

The Blockchain

酷毙了。考验我们区块链的时候到了。创建一个 blockchain.test.js 文件。

让我们添加几个测试用例。

我们将创建两个变量`blockchain`，它们也将用于添加块。现在让我们测试一下`addBlock()`函数。

Sweet tests

运行测试，现在您将看到 4 个测试通过。

接下来，我们将向区块链添加更多功能来支持多个参与者。

[第三部分:用 PoW 共识算法实现区块链和加密货币](/coinmonks/part-3-implementing-blockchain-and-cryptocurrency-with-pow-consensus-algorithm-d9b8cb928e3e)

*感谢您的阅读。* ***在下一部分中，我们将编写代码来验证区块链并添加对多个矿工的支持。*** *希望你喜欢编码。如果你发现这很有帮助，请鼓掌。*

如果你对区块链、以太坊或整个世界有任何疑问，请发表评论。:)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)