# 本周我从坚固、松露和网络中学到的 3 件事

> 原文：<https://medium.com/coinmonks/3-things-i-learned-this-week-using-solidity-truffle-and-web3-a911c3adc730?source=collection_archive---------1----------------------->

![](img/0dfe7962fe6105d904e2e99a076b6549.png)

Eye-candy so you’ll like my article ❤

当我开始开发时，很难找到简单问题的答案。在这里，我列出了我的问题和它们的答案。

在本文中，我将回答这些问题:

1.  如何向智能合同功能发送以太网？
2.  我如何生成一个随机数？
3.  如何在智能合约中记录事件，然后在事件发生时读取它们？

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)

智能合同功能？

有一些关于向智能合约发送以太网的非常好的文档，但它已经经历了一些迭代。此外，如何向合约交付资金取决于您使用的媒介。

如果你试图通过另一个智能合约将以太发送到一个智能合约，在[以太坊栈交易所有一篇关于这个过程的很好的文章，日期是 2016 年 8 月 22 日。](https://ethereum.stackexchange.com/questions/2876/how-does-one-contract-send-ether-to-another-contract-with-more-than-2300-gas)

如果你试图通过 web3 或 Truffle 控制台发送以太网，我是这样做的:

首先，您的智能合约必须包含一个*应付款*函数。

这是一个构造函数的示例，您可以在其中用 Ether 初始化智能协定:

```
Solidity: function Contract() payable { }
```

以下是智能合同中一些可支付功能的示例:

```
Solidity:function payableFunction() payable { }function payableFunction(uint randomVal) payable returns (bool) { }
```

以下是如何从 web3 或 Truffle 控制台调用这些函数:

```
truffle(development)> contract.payableFunction({from: address, value: etherAmount})truffle(development)> contract.payableFunction(randomVal, {from: address, value: etherAmount})
```

难住我的是我如何调用函数来发送 Ether *和*给它一个任意值。原来您首先传递您的函数参数，然后是一个包含您的 *from* 和 *value* 变量的字典(它们映射到 Solidity 中的 msg.sender 和 msg.value)。

# 如何在智能合约中生成随机数？

由于区块链是确定性的，随机数实际上是不可能产生的。在大多数赌博合同中实施的产业实力解决方案(其中大量资金依赖于 RNG)通常依赖于基于预言或票证的解决方案，其中多个玩家提交散列，然后对所有提交进行散列，然后显示强伪随机生成的数字。

我个人走的是简单的一条直线路线，至少在我的应用程序发展到可以处理更多以太网之前是这样:

```
Solidity: // Generate a random number from 1-MAX_VALUE (MAX_VALUE is set 
// arbitrarily by the developer). uint random_number = uint(block.blockhash(block.number-1))% MAX_VALUE + 1;
```

# 我如何处理智能合约中的事件？

事件是以太坊开发中一个众所周知的棘手、复杂的主题。我想出了如何处理一个错误条件，并以下面的方式记录一个事件。

首先，在您的 Solidity smart 合同中定义一个事件:

```
event Error(address indexed _sender, bytes32 error);
```

*索引*关键字很重要，因为这是我们通过调用合同函数的帐户来跟踪事件的方式。

以下是在合同函数中调用事件的方式:

```
Error(msg.sender, "ERROR_DETAILS_HERE");
```

下面是我们如何在 Truffle 控制台中使用 web3 来跟踪事件:

```
truffle(development)> var errorEvent = contract.Error({_sender: web3.eth.coinbase})truffle(development)> errorEvent.watch(function(err, result){ console.log(result.args) })
```

然后，当我们调用函数并引发一个事件时，我们的观察器可以获取它、读取它并做出相应的响应。

希望这有所帮助！我正在整理一篇关于开发的更全面的文章，但是将会发布我在这个过程中学到的东西。

## 如果你喜欢这篇文章，请推荐并分享给你的朋友！

## 在 Twitter 上联系我:@ [valkn0t](http://www.twitter.com/valkn0t)

[![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)