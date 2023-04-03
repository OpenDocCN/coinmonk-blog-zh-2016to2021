# 使用 web3 和 Haskell 的 Bytecraft

> 原文：<https://medium.com/coinmonks/bytecraft-with-web3-and-haskell-f91244f1d36e?source=collection_archive---------10----------------------->

*(此贴在*[*github*](https://github.com/antonbobkov/city_building_game_v1/tree/master/hs/minecraft-viewer)*)上也有*

![](img/d8ff46eb72b8b3c86ae3bfbd3e2d8f5a.png)

在这篇文章中，我们将回顾如何使用 Haskell [web3 包](https://hackage.haskell.org/package/web3)来制作 [bytecraft](http://bytecraft.club/) 图像生成器。我们在这里还使用了其他几个包，我将只介绍必要的部分。我们在这里的主要目标是介绍如何在 Haskell 中使用 web3 包。

首先，我们需要加载合同的 abi:

这是 Haskell web3 模块的魔法酱。它使用模板 Haskell 来生成调用我们的智能契约的类型检查的 Haskell 方法。这就是 Haskell 成为编写智能合约应用程序的绝佳语言的原因！

函数名与 ABI 中的相匹配。我发现定义一个错误类型的方法来获取签名很有帮助。例如，在我们的例子中，这个

给出此误差，单位为千兆赫:

在这种情况下，`getUpdateTimes`是一个采用`Call`类型的函数，并返回包装在`Web3`类型中的`ListN 1024 (UIntN 256)`。我们将很快打开所有这些类型的包装。现在，我们知道这个函数与我们的 solidity 契约中的以下函数相匹配:

我们的智能合同允许用户从 1024x1024 的网格中获得 32x32 的像素块。每个块是 1024 个像素，有 1024 个块。要将 32x32 的 8 位图像上传到块上，用户必须在块上下注。如果块上已经有一个股份，新的股份必须大于前一个，在这种情况下，前一个股份将返回给它的所有者。

该模块中的主要方法是`query`:

它接受 3 个网络参数:输出的文件名前缀、契约地址和 web3 提供者 URL。它还接受表示为 [repa](https://hackage.haskell.org/package/repa-3.4.1.3/docs/Data-Array-Repa.html) 数组的输入图像。这是最后一个处理的图像，它将被修改以包含自上次调用此函数以来所有更新的块。最后，作为一个`IO`操作，它返回一个新更新的图像。

在这个方法中，我们要做的第一件事是设置一些助手来帮助调用 web3 api:

因为我们没有在免费的 GCloud 实例上运行自己的完整节点，所以我们将依赖外部 http 提供者。我们的连接需要是 [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) ，所以使用`http-client-tls`包中的`newTlsManager`是必要的。

接下来我们设置`callData`，它有我们之前看到的`Call`类型。`Call`是一个记录类型，包含关于如何进行 web3 调用的信息。在我们的例子中，我们只关心`callTo`参数。有趣的语法来自`data-default`包，它为其他参数提供默认值。

`provider`是我们将 web3 RPC 调用发送到的地方。最后`doW3`使用我们刚刚创建的管理器和提供者发出请求。`doW3`用我们刚刚创建的管理器和提供者调用`runWeb3With`。

我们之前看到过`Web3`单子。它包装一个 web3 RPC 调用，并且`[runWeb3With](http://hackage.haskell.org/package/web3-0.7.3.0/docs/Network-Ethereum-Web3-Provider.html#v:runWeb3With)`在`IO`单子内执行`Web3`单子。

接下来，我们轮询每个区块更新的最后一个块号。我们还对契约本身进行最后一次轮询，以便与中的缓存值进行比较..lastUpdate.json ":

这里我们看到了我们的第一个 web3 调用！使用`doW3`和`callData`我们将得到一个`m (Either Web3Error a)`类型的值。在这个例子中我们不做任何错误处理，所以你会经常看到类似`either throw id updateTimes'`的表达式。这为我们提供了数据，但它属于`ListN 1024 (UIntN 256)`类型。这些来自`[Network.Ethereum.ABI.Prim](http://hackage.haskell.org/package/web3-0.7.3.0/docs/Network-Ethereum-ABI-Prim.html)`模块，其中包含所有 EVM 类型的表示。

如果你需要 EVM 类型的复习，这是一个检查[可靠性文档](http://solidity.readthedocs.io/en/v0.4.24/)的好时机。

回头看看 `getUpdateTimes`的定义，我们看到实度类型`uint[1024]`变成了`ListN 1024 (UintN 256)`。这是一个 256 位无符号整数的 1024 元素列表，两个大小都固定在类型级别！`ListN`是`IsList`的一个实例，其类型系列`Item`被实现为

其中有方法

让我们更熟悉的`[Int]`使用

`UIntN`是`Integral`的一个实例，所以我们可以使用`fromIntegral`来转换它。

好吧！因此，现在我们从智能合约中获得了第一个 Haskell 原语！接下来，我们想从每个块中获取实际的图像数据:

使用`updateTimes`我们构建了一个我们想要查询的块的`(x,y)`索引列表。这些查询被分组为 8 个一批(使用助手函数`group :: Int -> [a] -> [[a]]`)，并使用`monad-parallel`包中的`forM`并行执行。如果一次发出的请求太多，提供程序就会开始拒绝请求，并且按顺序运行这些请求会非常慢。8 运行得够快了，我的治疗师/治疗师还没有拒绝我们的任何请求。

在 `queryBlock`函数中，您将看到一个更有趣的转换:

分别使用`abiGet`和`abiPut`可以将所有 ABI 原语转换为`Get`和`Put`的实例(来自`binary`包)。`runPut (abiPut color)`将代表原始 8 位图像数据的 ABI 原语`ListN 32 (BytesN 32)`转换为`ByteString`。`Web3`是`MonadIO`的一个实例，所以我们也用`liftIO (putStrLn ...)`做一些进度记录。

稍后，我们将遍历表示为`repa`数组的 32 位图像，将原始的 8 位图像数据转换并复制到正确的位置。该操作所需的所有数据都存储在`ChunkInfo`中

回到`query`方法。我们还有一些簿记工作要做。我不会在这里赘述，因为它不是 web3 特有的。最后一步是运行前面描述的图像转换过程，并返回输出:

就是这样！我希望你像我一样喜欢编写自己的 web3 Haskell 应用程序。或许试试我们的智能合同。

bytecraft 契约拥有任何被标在瓷砖上的以太。我们拥有任何到这里的以太***0x 0d8a 07 e 01 FD 9 B3 da 4 ce 78109 DBD FD 385 be 59 BAE 2***