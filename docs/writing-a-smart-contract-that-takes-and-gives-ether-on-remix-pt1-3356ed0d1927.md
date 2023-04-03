# 撰写一份智能合同，接受和给予以太网的混合[PT1]

> 原文：<https://medium.com/coinmonks/writing-a-smart-contract-that-takes-and-gives-ether-on-remix-pt1-3356ed0d1927?source=collection_archive---------8----------------------->

项目参数:

1.  使用加密神谕获取以太坊的美元价格
2.  创建功能，允许用户存款至少 500 美元的乙醚到我们的合同
3.  创建允许用户从函数中取钱的函数
4.  用 Remix 编写原始合同，然后将项目转换为 truffle 项目，编写测试迁移，并将其从 VE 部署到 Testnet

好的，这很一般，但是这和微小的状态变化有很大的不同，我们开始吧:

大声思考让我们从第一个参数开始，我们需要什么？

1.  对于初学者，我们需要使用一个加密预言，让我们使用链式链接
2.  我们必须弄清楚如何根据神谕中的信息来计算以太坊的价格
3.  创建一个支付函数，允许用户存入给定数量的乙醚并持有它。

让我们从实现价格获取功能开始。让我们从关于获取最新价格的 [Chainlink 文档](http://docs.chain.link/docs/get-the-latest-price/)开始。

如果您想在合同中实现获取价格功能，首先必须导入合同本身。合同的顶部应该如下所示

```
//SPDX-License-Identifier: MITpragma solidity ^0.8.7;import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";
```

import 语句所做的是，它实际上引用了 chainlink Github，并获得了允许我们获取价格的合同。

现在到我们的实际合同，现在我们将在混音工作，并转移到一个松露项目。所以让我们开始用函数来计算价格。下面我一行一行的解释。

```
contract getPaid {address payable public owner;
address[] public funders;constructor() {
   owner = payable(msg.sender);function getThePrice() public view returns (uint256) {
//note that the contract address used is the ETH / USD price from the Kovan TestNetAggregatorV3Interface priceFeed = AggregatorV3Interface(0x9326BFA02ADD2366b30bacB125260Af641031331);
(,int256 price,,,) = priceFeed.latestRoundData();
return uint256(price);}}
```

关于变量和构造函数的一点小注意:

1.  第一个变量是一个空变量，它将被设置为与契约交互的任何人的地址。
2.  那么 funders 数组是一个只存储地址的数组，当我们需要实现取款功能时会用到它。
3.  如果你使用的是 V8 编译器，那么不要忘记让 owner 变量是可支付的，因为在 V8 中 msg.sender 不是自动可支付的

好的，这是一个强大的小函数，一旦它被部署，每次我们调用它时，它都会为我们获取价格:

1.  函数声明行声明函数名以及函数类型，它是公共的，意味着它可以被任何人调用，它不改变状态，所以它是一个视图函数，最后，它返回数据类型 uint 256[无符号整数 256 位]。
2.  然后 priceFeed 变量被声明，你看到它的类型是 AggregatorV3Interface，他妈的？在我们的例子中，你实际上可以从导入的接口声明新的对象，因为如果你在这里看实体文件[，你会看到文件从接口开始。TF 是接口？你可以把它想象成一个预装了半生不熟的功能的契约，半生不熟的意思是，你必须明确地告诉 solidity 你希望它如何与那些功能交互。](https://github.com/smartcontractkit/chainlink/blob/master/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol)
3.  PriceFeed 变量被设置为等于接口的一个实例，该实例指向监控 ETH / USD 对的 Kovan 测试网上的一个合约。你明白我说的半生不熟是什么意思了吗？我们必须告诉接口与什么契约地址进行交互]
4.  然后我们有了这段奇怪的代码，它以一个括号、一个逗号开始，后跟数据类型 int256 price 和另外 3 个逗号。因此，如果你去合同底部查看函数“latestRoundData ”,你会看到该函数有 5 个返回值，它们都有各种信息，如更新时的 RoundID 和其他信息，但对于我们的情况，我们只需要价格。您可能会问 Sonny，为什么我们不能将 priceFeed 设置为 int256 price。如果函数只返回一个值，你可以这样做，但是因为它返回 5，我们需要逗号和我们返回的值加到 5 上。因为我们不在乎返回它们，所以我们不需要填写它们，我们只需要逗号，这样它就知道我们想要的价格。
5.  然后，我们将该值强制转换为 256 位无符号整数并返回它。现在，如果你把它部署在 Kovan 或你正在使用的任何 testnet 上，你会看到一个比你预期的要长得多的数字。如果您查看每个合同地址的 chainlink 文档，每对地址都有一个返回的小数位数，因此在我们的例子中，它返回 8 个小数点，因此如果您向右数 8 个数字，您将得到以美元为单位的价格。

既然我们已经创建了价格获取函数，现在我们可以使用它来强制执行我们的智能合约的最小存款值，让我们选择 100 美元，这样任何低于该值的金额都将被我们的合约拒绝。但是我们需要第二个助手函数，它将把给出的价格转换成实际的美元计价的价格

1.  让我们先想想这个，之前我说过，事物被命名为 8 个小数位，所以如果我们除以 10 ^ 8，我们应该得到正确位置的小数，我们有一个美元换算。让我们把这个编码起来

```
function ConvertPriceToUSD() public view returns (uint256){uint256 currPrice = getThePrice();return  (currPrice / 10 ** 8);//this should move the decimal place enough to get a denomination of ETH in dollar terms
}
```

现在我们有一个方法来获得当前的价格是美元，稍后我们可以使用它来比较下面的资金函数中存放的 ETH 的数量。还要确保在 th:

```
function deposit() public payable {uint256 minimumAmt = 100 * 10  ** 18;require(msg.value >= minimumAmt, "Deposit more fool");addressToDollarAmt[msg.sender] += msg.value;funders.push(msg.sender);}
```

好吧，让我们一行一行地分析这个函数的功能:

1.  所以在第一行你会看到一个新的关键字，那就是 payable，这是我们告诉 Solidity 编译器这个函数在某种程度上处理货币交换的方式。
2.  然后，我们声明一个函数绑定变量，它设置了一个最小的阈值，要在下面的行中进行测试，我们将它乘以 10 18，以 WEI 表示
3.  下面我们有一个 require 语句，说明 100 wei 是该合约的最低门槛，如果他们的存款少于该值，他们会收到一条错误消息。
4.  然后这个地址被推送到 funders 数组。

太棒了，我们已经为多个用户实现了存款功能，但是如果我们的用户想从合同中取钱怎么办，我们如何确保只有他们可以提取他们投入的钱，而不是其他任何人的钱？

我们需要一些东西，第一个是修饰符，第二个是实际的撤销函数本身:

```
modifier onlyOwner {require(msg.sender == owner);_;}function withdraw() public payable onlyOwner {payable(msg.sender).transfer(address(this).balance);for (uint256 funderI=0; funderI < funders.length; funderI++) {address withdrawlAddy = funders[funderI];addressToDollarAmt[withdrawlAddy] = 0;}funders = new address[](0);}
```

1.  首先，我们从我们的修饰符开始，它只是一个额外的必需参数，我们可以用在我们的撤销函数中。不要错过“_”，这样会抛出一个异常，而不是破坏契约。
2.  然后，正如您在声明行中看到的，我们使用修饰符 only owner，然后当您转到第二行时，我们将 msg.sender 变量设为变量类型，然后我们调用 transfer 方法将合同的余额传输回与合同交互的地址。
3.  不要忘记，我们还必须更新阵列的状态，以确保我们合同的安全性。因此，我们开始一个循环来遍历 funders 数组，并将数组中的所有值都设置为 0[因此，只有该数组包含与之交互的人的地址]，然后启动一个新的 funders 数组，并将该新数组中的所有值都设置为 0。

现在，你已经完成了合同，现在是时候搬出 kushy 沙盒，这是重新组合，并将其转移到松露。如果你有任何未解决的错误，请查看这个 GitHub 报告。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 也阅读

[](/coinmonks/leveraged-token-3f5257808b22) [## 杠杆代币[多头代币]终极指南

### 杠杆化令牌是具有杠杆化风险敞口的 ERC20 令牌，不考虑保证金、要求、管理…

medium.com](/coinmonks/leveraged-token-3f5257808b22) [](https://blog.coincodecap.com/bitsgap-review) [## 获取信号、交易机器人和套利

### 在本文中，我们将讨论 bits gap——一个满足您所有交易需求的一站式加密交易平台…

blog.coincodecap.com](https://blog.coincodecap.com/bitsgap-review) [](https://blog.coincodecap.com/best-telegram-channels) [## 40 个最佳电报频道，用于加密、电影、表演和演讲| CoinCodeCap

### 编辑描述

blog.coincodecap.com](https://blog.coincodecap.com/best-telegram-channels) [](https://blog.coincodecap.com/best-social-trading-platforms) [## 5 个最佳社交交易平台[2021] | CoinCodeCap

### 编辑描述

blog.coincodecap.com](https://blog.coincodecap.com/best-social-trading-platforms) [](https://blog.coincodecap.com/blockfi-review) [## BlockFi 评论 2021:利弊和利率| CoinCodeCap

### 编辑描述

blog.coincodecap.com](https://blog.coincodecap.com/blockfi-review) [](/coinmonks/buy-bitcoin-in-india-feb50ddfef94) [## 如何在印度购买比特币？2021 年购买比特币的 7 款最佳应用[手机版]

### 如何使用移动应用程序购买比特币印度

medium.com](/coinmonks/buy-bitcoin-in-india-feb50ddfef94) [](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b) [## 加密税务软件——五大最佳比特币税务计算器[2021]

### 不管你是刚接触加密还是已经在这个领域呆了一段时间，你都需要交税。

medium.com](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b) [](https://blog.coincodecap.com/best-hardware-wallet-bitcoin) [## 存储比特币的最佳加密硬件钱包[2021] | CoinCodeCap

### 编辑描述

blog.coincodecap.com](https://blog.coincodecap.com/best-hardware-wallet-bitcoin) [](/coinmonks/pionex-review-exchange-with-crypto-trading-bot-1e459d0191ea) [## Pionex 评论 2021 |免费加密交易机器人和交换

### Pionex 是为交易自动化提供工具的后起之秀。Pionex 上提供了 9 个加密交易机器人…

medium.com](/coinmonks/pionex-review-exchange-with-crypto-trading-bot-1e459d0191ea)