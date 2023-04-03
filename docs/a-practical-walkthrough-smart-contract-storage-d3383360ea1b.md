# 智能合同存储的(实用)演练

> 原文：<https://medium.com/coinmonks/a-practical-walkthrough-smart-contract-storage-d3383360ea1b?source=collection_archive---------0----------------------->

在开始之前，已经有两个关于这个主题的极好的来源，它们很好地涵盖了大多数基本用例:

这个很棒，但是写得很扎实，这意味着不容易弄脏手进行测试:

[](https://programtheblockchain.com/posts/2018/03/09/understanding-ethereum-smart-contract-storage/) [## 了解以太坊智能合约存储

### 以太坊智能合约使用一种不常见的存储模型，这种模型经常让新开发人员感到困惑。在这篇文章中，我将描述…

programtheblockchain.com](https://programtheblockchain.com/posts/2018/03/09/understanding-ethereum-smart-contract-storage/) 

第二个是用 javascript 编写的，但是非常简短，没有任何测试:

[](/aigang-network/how-to-read-ethereum-contract-storage-44252c8af925) [## 如何阅读以太坊合约存储

### 每个人都在谈论合同中的数据是公开的，但不是每个人都知道如何阅读它。

medium.com](/aigang-network/how-to-read-ethereum-contract-storage-44252c8af925) 

本教程的目的是使用 truffle 采取一种更加测试驱动的方法，以便充分了解这些不同的数据结构是如何存储的。在这个过程中，我们还将创建一些简洁的工具。

> [发现并回顾最佳区块链软件](https://coincodecap.com)

本教程不会介绍所有可能的存储方法，它将介绍一个包含许多但不是所有情况的暂停列表。值得一提的一种类型是数组。我们将查找并测试以下类型:

1.  布尔运算
2.  尤因茨
3.  用线串
4.  映射
5.  嵌套映射
6.  地址

如果您迷路了，可以在这里找到本教程的完整代码:

[](https://github.com/TovarishFin/storage-coin-tutorial) [## TovarishFin/存储-硬币-教程

### 存储-硬币-教程-代码存储教程发现在 https://medium.codylamson.com

github.com](https://github.com/TovarishFin/storage-coin-tutorial) 

说到这里……让我们开始吧。

## 项目设置

如果您还没有全球安装 truffle:

```
yarn global add truffle
```

创建目录并输入:

```
mkdir contract-storage && cd contract-storage
```

启动松露项目:

```
truffle init
```

让我们创建将用于测试的契约:

```
touch contracts/StorageCoin.sol
```

我们将使用 OpenZeppelin 的 ERC20 令牌来实现一些易于使用的存储，这可能更有关联:

```
yarn add openzeppelin-solidity
```

## 写合同

让我们使用 OpenZeppelin 的`PausableToken`创建一个简单的例子。将以下内容粘贴到`contracts/StorageCoin.sol`中:

如果你不熟悉`PausableToken`，不要担心。在下一节中，我们将进一步研究它以及它所继承的任何契约。

这个契约将很好地完成，因为我们将使用以下数据类型:

1.  弦`name` & `symbol`
2.  uint `totalSupply_`不是`decimals`是`private totalSupply_`
3.  布尔`paused`
4.  地址`owner`
5.  映射`balances`
6.  嵌套映射`allowance`

**上面第二条有一些有趣的旁注:**

小数不会被存储访问的原因是因为它不在那里！变量存在于代码中，而不是存储中。这允许一个人用这个做一些非常酷的把戏，我稍后会讲到。

我们将访问`private totalSupply_`的原因是，嗯，这是可能的。`private`任何对存储稍有了解的人都完全可以接触到变量。明确地说，这意味着当标记为私有时，您不应该假设用户无法访问任何内容。

## 准备编写测试

在这一点上，有必要提一下存储是如何工作的。契约存储本质上类似于从`bytes32`到`bytes32`的映射。密钥可以很容易地解释为将字节转换为小数的数字:

值从 0 开始按顺序存储:

```
0x00000000000000000000000000000000000000000000000000000000000000000
```

一直到⁵⁶或者 1.157920892e77:

```
0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff
```

给最后一个数字更多的背景…这是一个令人难以置信的巨大数字。地球上的原子比这个数字还少。

存储是按照合同代码中出现的顺序依次设置的。所有的存储都是以字节存储的，这意味着[了解 ascii 如何工作可能是个好主意](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/)(字符串是以 ascii 而不是 unicode 编码的)。

记住这一点，让我们找出变量在我们的契约中出现的顺序，包括继承。

我们打算全局添加`truffle-flattener`，以便更好地了解存储在哪里。这个包将一个从不同文件的多个协定继承而来的协定转换成一个文件。它对于在 [etherscan](https://etherscan.io/) 上验证合同也非常有用。

```
yarn global add truffle-flattener
```

让我们展平我们签订的合同，看看我们在这里做什么:

```
truffle-flattener contracts/StorageCoin.sol > StorageCoinFlattened.sol
```

在你的根目录中打开`StorageCoinFlattened.sol`。你应该有和这个一样的:

## 为简单存储编写测试

让我们创建测试文件:

```
touch test/StorageCoin.js
```

这是我们对`Storage.js`的基本布局:

现在我们有了布局，让我们创建我们的第一个工具！我们将创建一个函数来扫描指定数量的存储槽中的任何值。

创建一个新的`helpers.js`文件:

```
touch test/helpers.js
```

让我们添加我们的老朋友，`BigNumber`和`chalk`来使事情变得更漂亮:

```
yarn add "git+https://github.com/frozeman/bignumber.js-nolookahead.git" chalk
```

Truffle 使用旧版本，所以我们必须确保安装正确的版本。

现在打开`test/helpers.js`，让我们创建我们的第一个助手函数:

该函数将从第一个开始在存储槽中查找数据，直到连续 10 次只找到空槽。这里完成大部分工作的函数是`web3.eth.getStorageAt()`。这是我们从部署的智能合同中直接访问存储的方式。这也适用于 mainnet 或任何 testnets 上的合同。

最后，让我们使用这个函数。更新`StorageCoin.js`喜欢这样:

我们已经包含了助手函数和粉笔和工具。`chalk`只是为了颜色。为了正确注销对象，需要使用`util`。

运行`truffle test`看看我们有什么储物件。你应该得到这样的东西(但是有漂亮的颜色):

```
local variables:
totalSupply: 0x56bc75e2d63100000
name: 0x53746f72616765436f696e
symbol: 0x535443
owner: 0x627306090abab3a6e1400e9345bc60c78a8bef57
storage:
[ { slot: 0, data: '0x0' },
  { slot: 1, data: '0x056bc75e2d63100000' },
  { slot: 2, data: '0x0' },
  { slot: 3,
    data: '0x01627306090abab3a6e1400e9345bc60c78a8bef57' },
  { slot: 4,
    data: '0x53746f72616765436f696e000000000000000000000000000000000000000016' },
  { slot: 5,
    data: '0x5354430000000000000000000000000000000000000000000000000000000006' },
  { slot: 6, data: '0x0' },
  { slot: 7, data: '0x0' },
  { slot: 8, data: '0x0' },
  { slot: 9, data: '0x0' },
  { slot: 10, data: '0x0' },
  { slot: 11, data: '0x0' },
  { slot: 12, data: '0x0' },
  { slot: 13, data: '0x0' },
  { slot: 14, data: '0x0' } ]
```

让我们试着破译这个，看看这里是什么。

槽 0 是为平衡映射保留的空间。我们稍后将对此进行更深入的探讨。

如果我们查看插槽 1，我们可以看到，如果我们将十六进制值粘贴到工具中，它等于 100e18。这是我们的`totalSupply`。我们知道这是`totalSupply`，因为映射以不同的方式存储，并且已经在 slot 1 中。

像槽 0 一样，槽 2 是另一个映射。这是嵌套的`allowed`映射。我们稍后也会深入探讨这一点。

我们可以立即知道槽 3 包含所有者地址。但是还有别的东西在那里…这是`paused`布尔。真将是`0x01`，假将是`0x00`。Solidity 会尝试在一个储物格中放入多件物品。这正是这里发生的情况。

一旦将值粘贴到[十六进制到 ascii 转换器](https://www.rapidtables.com/convert/number/hex-to-ascii.html)中，就可以计算出插槽 4。这是`name`变量。敏锐的眼睛可能会注意到结尾的`16`。当把[十六进制转换成十进制](https://www.rapidtables.com/convert/number/hex-to-decimal.html?x=16)时，可以看到这是 22。22 是字符串中数据的长度(不带 0x 前缀)。

```
'53746f72616765436f696e'.length === 22
```

插槽 5 与插槽 4 的情况相同。当[转换成 ascii](https://www.rapidtables.com/convert/number/hex-to-ascii.html) 时，你会发现值是`STC`，我们的`symbol`变量。还可以看到数据的长度是 6，和末尾的值一样。

**存储按顺序存储**

在这一点上你可能会疑惑…我以为这些是按顺序存放的？现在仍然是这样…但是如果你真的经历了每个契约的继承，你会发现顺序是不同的。在`node_modules`打开你的`openzeppelin-solidity`目录，你会看到顺序是这样的:`ERC20/Basictoken -> StandardToken -> PausableToken -> StorageCoin`

你会看到`BasicToken`，第一个继承顺序是`balances`，后面是`totalSupply`。然后`StandardToken`有了`allowed`。

`PausableToken`继承自`Pausable`，而`Pausable`继承自`Ownable`，导致`paused`和`owner`成为下一个。

最后，添加我们在`StorageCoin`中设置的变量。`name`最先出现，因为它首先被声明，然后是`symbol`。请记住，常量不是存储在存储器中，而是存储在部署的代码本身中。所以`decimals`在仓库里无处可寻。

让我们将测试块变成一组真实的测试:

我们还需要在最后两个测试中使用的助手。把这个放到你的`helpers.js`文件里。请确保也将其导出:

这个函数从存储器中返回十六进制值的 ascii 版本。我们需要删除尾部的空字节以及末尾的长度声明。我们可以通过获取最后 2 个字符(长度)并使用它来表示切片应该在哪里结束(字符串的长度)来做到这一点。

## 了解映射和嵌套映射存储

映射不同于我们到目前为止看到的简单类型。当映射被声明时，像任何其他类型一样，空间被顺序地为它保留，但是实际的值被存储在不同的槽中。为了找到插槽，您必须获取与密钥连接的插槽的 sha3(keccak256)散列。

我们可以用我们将构建的获取映射值的助手来说明这一点。

首先，我们需要另一个依赖项:

`yarn add left-pad`

然后确保在`helpers.js`的顶部要求它:

```
const leftPad = require('left-pad')
```

之后，将这些函数放入你的`helpers.js`文件中并导出`getMappingSlot`:

让我们看看`getMappingSlot`。我们将槽和键都转换成十六进制(如果还没有的话),并且用空字节填充。他们两个都需要在本质上的`bytes32`。我们填充到 64，因为每个字节是两个字符。

然后我们从格式化的键和槽创建一个散列(总是键在前，槽在后！)这个散列是存储映射值的地方。一个 32 字节的散列可以被转换成一个数字。这个数字将在 0 到⁵⁶的范围内，如前所述，这是一个很大的数字。由于范围很大，一个贴图值几乎不可能与另一个值冲突。以这种方式设置映射是因为我们无法提前知道一个映射中会有多少元素。这样，它可以以非顺序的方式设置，其值不会被其他存储覆盖。

在`getMappingStorage`中，我们使用`getMappingSlot`并使用与之前相同的`web3.eth.getStorageAt()`函数。

# 映射存储测试

让我们使用刚刚创建的映射存储助手。将`helpers.js`中的`getMappingStorage`导入`StorageCoin.js`中，并在槽 1 测试前将该新测试块放入存储槽 0 中:

在这里，您可以看到我们正在检查所有者余额是否与`totalSupply`匹配。请记住，在合同的构造者中，我们给了所有者全部供应。我们可以使用`BigNumber`将一个十六进制值转换成一个数字。然后，我们可以确保这与`totalSupply`匹配。

亲自运行`truffle test`以查看所有测试是否通过。

# 了解嵌套映射存储

在我们的`StorageCoin`契约中，我们还有一个名为`allowed`的嵌套映射，比如:`mapping(address => mapping(address => uint256))`。这基本上遵循与常规映射相同的模式，但是是以递归的方式。让我们再次进入助手函数，看看它是如何工作的。我们将添加一个名为`getNestedMappingStorage`的新函数，它将递归运行我们已经创建的`getMappingStorage`函数。使用新函数后，您的`helpers.js`文件应该类似于下图:

如你所见，这个函数没什么好花哨的。它只运行两次`getMappingSlot`函数，获取槽和密钥的散列，然后获取该散列并用第二个密钥对其进行散列。

让我们在`StorageCoin.js`的测试中使用这个新函数。为了看到价值，我们需要设置一个余量。为此，我们还需要解除令牌的暂停。让我们同时为这两个步骤编写测试。以下是更新后的测试文件:

测试文件已通过以下方式进行了更新:

1.  在测试开始时删除了 simpleStorage 的日志
2.  在`owner`之后，将支出者添加到顶级测试中
3.  增加了取消暂停的测试
4.  增加了设定容差的测试
5.  添加了查找嵌套映射存储的测试

运行`truffle test`并检查测试是否通过。您应该会得到与此类似的结果:

```
when accessing StorageCoin storage
    Contract: StorageCoin
      ✓ should have owner balance in slot 0 mapping (88ms)
      ✓ should have totalSupply in slot 1
      ✓ should have paused in slot 3
      ✓ should have owner in slot 3 as well
      ✓ should have name in slot 4
      ✓ should have symbol in slot 5
      ✓ should unpause the token as owner (56ms)
      ✓ should set allowance for spender as owner (83ms)
nested mapping slot:
0xded101565a23504cd4339827add3a8b16f30ceff2912c1b6ef015848eade7942
nested mapping value slot:
0xd40f44dd33ce83ebf23375ff0b844b9aa265bbdfdf680d1743d07ed5bca013e5
nested mapping value storage:
0x015af1d78b58c40000
allowance as number:
25000000000000000000
      ✓ should have owner allowance for spender at correct storage slot (109ms)
```

您还将看到嵌套映射的不同值。我们之前已经知道你的津贴槽在槽 2 中，所以这是我们的第一个映射槽。然后，我们获取该值并用`owner`对其进行散列，并为嵌套映射槽打印该值。这是我们嵌套映射存在的地方，就像我们的第一个映射存在于 slot 2。然后我们取这个值并用`spender`散列它以得到我们的嵌套映射值 slot。这是实际的存储位置。嵌套映射值存储是十六进制的实际值。通过将它放入 BigNumber 中将其转换为小数，我们得到最后一个值，即`owner`给予`spender`的余量的小数。

在测试本身中，我们获取十进制值，并将其与通过 getter 函数`allowance()`从正常检索中获得的值进行比较。

做完这些后，如果你想保持整洁，就把它们拿掉😀。

# 为映射构建一些简洁的工具

我们已经找到了所有的储物空间……但是当有人牵着你的手时，这就简单多了。如果你在看一份完全不同的更复杂的合同呢？可能很难知道映射应该在哪个槽。

让我们创建一对查找映射和嵌套映射的查找函数。将这两个函数添加到您的`helpers.js`文件的底部，并记住导出它们:

对于这些功能，没有太多的解释。我们只是从开始槽到结束槽迭代槽，并返回我们找到的任何值。

让我们在测试文件中使用它。将这两个测试添加到测试块的末尾:

正如你所看到的，我们对之前的相关函数给出了相同的参数。我们只是将它设置为从槽 0 到 20 检查每个函数。这对于寻找复杂的存储非常有用。

运行`yarn test`查看结果，并检查测试是否通过。

# 结论&下一步何去何从

当访问存储时，嵌套映射可能会变得复杂。但是，在本教程中，我们没有涉及数组。如果您想了解如何检索其他类型。我建议要么查看这篇文章顶部的链接，要么查看关于此事的[官方可靠性文档](http://solidity.readthedocs.io/en/v0.4.24/miscellaneous.html#layout-of-state-variables-in-storage)。它也值得重复，因为它只是简单地提到:如果两个变量都可以放入一个 32 字节的槽，它们将被打包到同一个槽中。我们看到布尔型和地址打包在一起。查看储物件时，请记住这一点！

你现在应该有了(大部分)检查任何合同存储的工具，不管是不是私有的。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [币安交易机器人](/coinmonks/binance-trading-bots-d0d57bb62c4c) | [OKEx 评论](/coinmonks/okex-review-6b369304110f) | [阿塔尼评论](https://coincodecap.com/atani-review)
*   [最佳加密交易信号电报](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b) | [MoonXBT 评论](/coinmonks/moonxbt-review-6e4ab26d037)
*   [如何在 Bitbns 上购买柴犬(SHIB)币？](https://coincodecap.com/buy-shiba-bitbns) | [购买弗洛基](https://coincodecap.com/buy-floki-inu-token)
*   [CoinFLEX 评论](https://coincodecap.com/coinflex-review) | [AEX 交易所评论](https://coincodecap.com/aex-exchange-review) | [UPbit 评论](https://coincodecap.com/upbit-review)
*   [十大最佳加密货币博客](https://coincodecap.com/best-cryptocurrency-blogs) | [YouHodler 评论](https://coincodecap.com/youhodler-review)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   [面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [免费加密信号](/coinmonks/free-crypto-signals-48b25e61a8da) | [加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [杠杆代币](/coinmonks/leveraged-token-3f5257808b22)终极指南