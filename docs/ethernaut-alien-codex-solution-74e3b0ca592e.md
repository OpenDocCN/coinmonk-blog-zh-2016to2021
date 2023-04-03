# 以太外星人法典解决方案

> 原文：<https://medium.com/coinmonks/ethernaut-alien-codex-solution-74e3b0ca592e?source=collection_archive---------3----------------------->

[外星人法典](https://ethernaut.zeppelin.solutions/level/0x73048cec9010e92c298b016966bde1cc47299df5)是[妮可朱](https://medium.com/u/60cd0f45aab?source=post_page-----74e3b0ca592e--------------------------------)设计的以太 20 级。本文提出了我的解决方案。

![](img/541a1e601d8b525ffab46887294f0830.png)

**剧透警报。如果你想自己解决，请不要阅读下面的内容。下面的文本包含完整的解决方案。**

关卡的目标是声明契约的所有权。AlienCodex 是从 Ownable 继承的，因此为了做到这一点，我们希望用我们的地址覆盖契约存储中的`_owner`变量。
整个解决方案可以分为两部分。首先，我们必须以某种方式将`contract`变量设置为 true 来访问契约方法，因为它们受到`contacted`修饰符的保护。

# 将联系人设置为 True

方法`make_contact`将`contact`设置为真，但是有一个检查`assert(_firstContactMessage.length > 2**200);`。显而易见的解决方案是传递长度大于`2 ** 200`的`bytes32[]`数组。问题是数组的大小是如此之大，以至于没有机器有足够的内存来存储它。如果您尝试这样做，它会使您的本地节点崩溃。
能做些什么？有人可能会问。一如既往的细节魔鬼。Solidity 此时不会生成验证有效载荷大小与声明长度的代码。换句话说，当你调用 solidity 方法时，你传递的是数组大小，而不是实际有效载荷。

让我们滥用它。

[约定 ABI](https://solidity.readthedocs.io/en/latest/abi-spec.html) 规范定义了我们应该如何为约定调用编码数据。在我们的例子`make_contact(bytes32[] _firstContactMessage)`中，我们有一个带有一个动态参数的函数。根据规范，数据参数将具有以下结构:

*   函数签名的 4 字节哈希
*   bytes32[]数据部分的位置
*   bytes32[]数组的长度
*   实际数据。

让我们计算所有的值。
函数签名的散列将是`web3.sha3('make_contact(bytes32[])')`，这导致`0x1d3d4c0b6dd3cffa8438b3336ac6e7cd0df521df3bef5370f94efed6411c1a65`。我们要取前 4 个字节，所以`0x1d3d4c0b`我们想要的结果。
数据的位置从签名的散列值偏移，在我们的例子中，它只有 32 个字节，因为 ABI 规范中的长度是 32 个字节—`0x0000000000000000000000000000000000000000000000000000000000000020`。
数组的长度必须大于`2**200`让我们用`0x1000000000000000000000000000000000000000000000000000000000000000`吧。实际的数据实际上什么都没有，因为我们违背了承诺，不会为数组提供任何数据。

最终数据将是

最后，我们可以调用`make_contact`方法将`contact`设置为 true——

`sendTransaction({to: contract.address, data: data, from: player, gas: 900000});`。

`await contract.contact()`的结果应该变为`true`。

# 用玩家变量覆盖所有者

由于`contact`没有被设置为`true`，我们可以访问所有被`contacted`修饰符保护的方法。
似乎没有办法修改`_owner`变量，因为不存在分配它的代码。但是请记住，所有状态变量都位于同一个存储连续体上，可能会成为写入错误的受害者。
`revise`功能可以将任何存储槽设置为我们提供的任何值。正是我们需要的。不幸的是，如果我们用 index > = length 调用它，它会失败。
方法`retract`没有检查 int 下溢。通过调用它，我们将把`codex`的长度从`0`改为`2**256 - 1`。EVM 的存储大小正好是`2**256-1`个槽的`32`字节。本质上，通过将`codex`的长度设置为 EVM 存储的长度，我们获得了修改整个 EVM 存储的任何插槽的能力。
我们必须计算出`_owner`变量在存储器上的位置以及偏移量索引，以便用`revise`方法对其进行修改。
`_owner`变量位于合约[存储](https://programtheblockchain.com/posts/2018/03/09/understanding-ethereum-smart-contract-storage/)的 0 slo。Codex 数组长度位于 1 个存储槽。这是因为 EVM 优化存储和地址类型占用 20 字节，bool 占用 1 字节，所以它们都适合一个 32 字节槽。
现在我们必须为`revise`方法计算索引`i`。根据分配规则，位于`keccak256(bytes32(1))`的 EVM 存储器上的`codex`阵列的开始。因此我们的目标位置将是`index = 2 ** 256 - uint(one);`。

运行后计算结果是`35707666377435648211887908874984608119992236509074197713628505308453184860938`。
现在我们终于可以调用`revise`方法了。

因此`await contract.owner();`应该改成你的地址。成功！

# 结论

该级别向我们展示了如何滥用契约 ABI 来为契约调用中的动态数组参数提供假长度，以及如何滥用存储的动态数组来用所需数据覆盖契约存储内的任何片段。

## 如果这篇文章有帮助，请点击拍手👏按钮下面几下，以示支持！⬇⬇

# 社会的

*   在 [LinkedIn](https://www.linkedin.com/in/ylv-io/) 上与我联系。
*   在[推特](https://twitter.com/ylv_io)上关注我。

# 阅读更多

[](https://hackernoon.com/how-to-create-and-deploy-your-own-eos-token-1f4c9cc0eca1) [## 如何创建和部署您自己的 EOS 令牌

### 我们将弄清楚什么是 EOS 令牌，以及您如何自己创建和部署它。

hackernoon.com](https://hackernoon.com/how-to-create-and-deploy-your-own-eos-token-1f4c9cc0eca1) [](https://hackernoon.com/how-much-does-it-costs-to-run-dapp-in-2018-87ee11fe1d5d) [## 2018 年办 DApp 要花多少钱

### 你认为你的 AWS 或网站的数字海洋账单正在杀死你吗？

hackernoon.com](https://hackernoon.com/how-much-does-it-costs-to-run-dapp-in-2018-87ee11fe1d5d) 

*最初发表于* [*ylv.io*](https://ylv.io/p/8bada813-7700-4d34-ad30-04d19a71b802/) *。*

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)