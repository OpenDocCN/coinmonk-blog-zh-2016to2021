# 使用 Nile 开始 StarkNet(适用于初学者)

> 原文：<https://medium.com/coinmonks/starknet-tutorial-for-beginners-using-nile-6af9c2270c15?source=collection_archive---------3----------------------->

![](img/425897b48c455b2ffe614bfe6878b86a.png)

在[以太坊](https://ethereum.org) L2 可伸缩性解决方案中，最有前途的方法之一无疑是**有效性汇总**，也称为[零知识汇总](https://ethereum.org/en/developers/docs/scaling/layer-2-rollups/#zk-rollups)。StarkNet 是实现 ZK 汇总的主要项目之一。

在本教程中，我们将使用 [Nile](https://github.com/martriay/nile) 为 [Cairo](https://www.cairo-lang.org/) 建立一个开发环境，该语言用于为 StarkNet 编写智能合同。

如果你想更多地了解 Cairo，我真的推荐你阅读 StarkNet 背后的公司 StarkWare 发布的[文档](https://www.cairo-lang.org/docs/hello_cairo/index.html)和 [Cairo 101](https://www.youtube.com/playlist?list=PLcIyXLwiPilV5RBZj43AX1FY4FJMWHFTY) 系列视频。

# 入门指南

如果是第一次在机器上安装 Cairo，请先安装`gmp`:

```
sudo apt install -y libgmp3-dev # linuxbrew install gmp # mac
```

> 如果你在苹果 M1 电脑上安装`gmp`有任何问题，[这里有一个潜在解决方案的列表](https://github.com/OpenZeppelin/nile/issues/22)。

我们将开始为我们的项目创建一个文件夹，并将`cd`放入其中:

```
mkdir myproject
cd myproject
```

目前，Cairo 需要一个 python 环境，所以我们将创建一个`[virtualenv](https://docs.python.org/3/library/venv.html)`并激活它:

```
python3 -m venv env
source env/bin/activate
```

现在我们有了自己的工作环境，可以安装`nile`:

```
pip install cairo-nile
```

快到了！因为`nile`会帮我们处理剩下的事情。只需执行:

```
nile init...
✨  Cairo successfully installed!
...
✅ Dependencies successfully installed
🗄 Creating project directory tree
⛵️ Nile project ready! ...
```

就是这样！Nile 为我们创建了项目目录结构并安装了 Cairo，以及其他依赖项(`ecdsa`、`pytest`等)。

您会看到类似这样的内容:

```
.
├── contracts
│   └── contract.cairo
├── tests
│   └── test_contract.py
└── Makefile
```

# 收集

Nile 附带了`contracts/contract.cairo`，一个智能合约示例:

让我们试着编写我们的合同:

```
nile compile📁 Creating artifacts/abis/ to store compilation artifacts
🤖 Compiling all Cairo contracts in the contracts/ directory
🔨 Compiling contracts/contract.cairo
✅ Done
```

如果您阅读输出的第一行，您会注意到该命令创建了一个包含编译输出的`artifacts/`文件夹，其中包括[ABI](https://www.cairo-lang.org/docs/hello_starknet/intro.html?highlight=abi#the-contract-s-abi):一个包含我们与 [StarkNet](https://www.cairo-lang.org/docs/hello_starknet/index.html) 智能合约交互所需的所有信息的文件。

注意，默认情况下，`nile compile`编译`contract/`下的所有内容，但是您可以将任何文件作为参数传递:

```
nile compile contracts/contract.cairo contracts/other_contract.cairo
```

# 测试

Nile 还为我们的`contract.cairo`合同提供了一个样本测试文件:

由于 Nile 还为我们安装了`pytest`和`pystest-asyncio`，我们可以开箱执行它，它将运行它在`tests/`下找到的任何测试:

```
pytest======================= test session starts ========================
...tests/test_contract.py .                                      [100%]...====================1 passed, 2 warnings in 2.07s===================
```

# 结论

就是这样！没那么难吧。我们已经能够:

*   安装开罗+尼罗河
*   设置开发环境
*   汇编合同
*   测试合同

当然，Cairo 和 StarkNet 仍然处于劣势，这使得掌握它们成为一个真正的挑战，但是 Nile 让整个交易变得容易多了。此外，StarkWare 团队正全力以赴，所以我预计这一切将在✨很快改善 100 倍

接下来，学习如何部署和管理您与 Nile 的合同。

*(2021 年 11 月 3 日)已更新，可与最新的 Nile 和 Cairo v0.5.0 一起使用。*

我是 [@martriay](https://twitter.com/martriay/) ，是 [OpenZeppelin](https://openzeppelin.com/) 的产品开发人员，也是[open zeppelin Cairo Contracts](https://github.com/OpenZeppelin/cairo-contracts)的首席开发人员，这是一个用 Cairo 编写的用于安全智能合约开发的库。(🧑‍💻嘘，我们要招聘！)

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 另外，阅读

[](https://blog.coincodecap.com/crypto-exchange) [## 最佳加密交易所| 2021 年十大加密货币交易所

### ICON _ PLACEHOLDEREstimated 预计阅读时间:28 分钟加密货币交易所的加密交易需要知识…

blog.coincodecap.com](https://blog.coincodecap.com/crypto-exchange) [](https://blog.coincodecap.com/crypto-lending) [## 2021 年 10 大最佳加密贷款平台| CoinCodeCap

### 当谈到加密货币贷款时，大量因素等同于良好的收入状况。此外，借款的一部分…

blog.coincodecap.com](https://blog.coincodecap.com/crypto-lending) [](/coinmonks/crypto-trading-bot-c2ffce8acb2a) [## 2021 年最佳免费加密交易机器人

### 2021 年币安、比特币基地、库币和其他密码交易所的最佳密码交易机器人。四进制，位间隙…

medium.com](/coinmonks/crypto-trading-bot-c2ffce8acb2a) [](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b) [## 最佳 4 个加密交易信号电报通道

### 这是乏味的找到正确的加密交易信号提供商。因此，在本文中，我们将讨论最好的…

medium.com](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b) [](https://blog.coincodecap.com/blockfi-review) [## BlockFi 评论 2021:利弊和利率| CoinCodeCap

### 今天，我们提出了一个全面的 BlockFi 评论，这是一个成立于 2017 年的加密贷款平台，拥有其…

blog.coincodecap.com](https://blog.coincodecap.com/blockfi-review) [](/coinmonks/buy-bitcoin-in-india-feb50ddfef94) [## 如何在印度购买比特币？2021 年购买比特币的 7 款最佳应用[手机版]

### 如何使用移动应用程序购买比特币印度

medium.com](/coinmonks/buy-bitcoin-in-india-feb50ddfef94) [](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b) [## 加密税务软件——五大最佳比特币税务计算器[2021]

### 不管你是刚接触加密还是已经在这个领域呆了一段时间，你都需要交税。

medium.com](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b) [](https://blog.coincodecap.com/best-hardware-wallet-bitcoin) [## 存储比特币的最佳加密硬件钱包[2021] | CoinCodeCap

### 保管您的数字资产很容易，但找到正确的存储方式却是一项繁琐的任务。在线钱包有一个风险…

blog.coincodecap.com](https://blog.coincodecap.com/best-hardware-wallet-bitcoin)