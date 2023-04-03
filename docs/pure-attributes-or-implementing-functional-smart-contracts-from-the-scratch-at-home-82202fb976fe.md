# 纯属性或在家从头开始实现功能性智能合约

> 原文：<https://medium.com/coinmonks/pure-attributes-or-implementing-functional-smart-contracts-from-the-scratch-at-home-82202fb976fe?source=collection_archive---------1----------------------->

TLDR:一个简单的数据模型，如果你想在家里从头开始实现一个新的智能合同机制，它会很有帮助。用 JavaScript 和函数式编程。

![](img/2b587f16235062cfe1290bc6cae6b150.png)

Photo by [Igor Ovsyannykov](https://unsplash.com/@igorovsyannykov?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 避免副作用

智能契约是关键的代码片段，应该易于编写和测试。

虽然目前在形式代码验证领域正在进行认真的开发，但是纯属性思想是关于使用现有的并且在行业中被广泛接受的函数式编程方法。

如果一个智能契约能够表现为一个没有副作用的函数，从而易于测试和推理，那会怎么样？它是如何工作的？

让我们做一些原型设计并创建一个函数，它可以作为一些虚拟加密货币令牌的非常简化的智能合约代码。

我们的函数将接收 3 个参数:

- **orig** 始发地账户地址
- **dest** 目的地账户地址
- **value** 从始发地向目的地转移的代币数量

source: [https://gist.github.com/slavasn/3ddacb1f522e76cadb8ee4f77ebd6f24](https://gist.github.com/slavasn/3ddacb1f522e76cadb8ee4f77ebd6f24)

*getAttribute* 和 *updateAttribute* 是系统内置函数，负责检索和更新与此智能合约相关的用户信息。我们对 *updateAttribute* 特别感兴趣，它表示在我们的传递函数中发生的副作用。

怎样才能去掉它，让函数体内部的系统状态没有变化？让我们试着写一个新版本的代码。

source: [https://gist.github.com/slavasn/10f36672bda08b7542a871bbe67d7a27](https://gist.github.com/slavasn/10f36672bda08b7542a871bbe67d7a27)

我们在这里做了什么，这意味着什么:

```
{
    target: dest,
    name: 'balance',
    value: destBalance + value
}
```

我们已经执行了我们的函数，并返回了计算结果，它说:*将目标地址****dest****的名为****balance****的属性设置为新值* ***destBalance +值*** *。*
底层代码会取它，检查返回值并保存计算结果。

## 代码合同

注意，在第二个例子中，没有对返回的余额进行边界检查。现在，我们可以从主代码逻辑中省略这些检查，以使其更加清晰，并将所有验证委托给特殊函数。

```
function validateBalance(balance) {
    return balance >= 0 && balance <= MAX_BALANCE;
}
```

将使用这些预定义的函数自动验证返回的属性列表，如果有效，帐户的属性*余额*将相应地更新为返回值。

## 使用函数式语言

这有什么意义？对于第一个代码示例，它也可以很好地工作，对吗？

使用无副作用的函数为使用真正的函数式语言打开了大门，这给我们的代码带来了额外的好处——没有了 *null* 和数据结构的不变性。

让我们用 Elm 重写这个小例子。

> 与手写的 JavaScript 不同，Elm 代码实际上不会产生运行时异常。相反，Elm 使用类型推断来检测编译过程中的问题。
> [http://elm-lang.org/](http://elm-lang.org/)

source: [https://gist.github.com/slavasn/ff7d531e6279199468ebd4347e6e02cd](https://gist.github.com/slavasn/ff7d531e6279199468ebd4347e6e02cd)

这可以极大地提高代码的可测试性和验证。这里以 Elm 为例。也有可能使用其他编译 JavaScript 语言，如 PureScript、TypeScript、Clojure。

## 属性坐标

我们现在有了两个坐标，可以让我们识别地址的属性——帐户**地址**，属性**名称**。

到目前为止一切顺利。但是，如果我们想创建几个智能合同呢？我们怎么知道调用什么函数呢？

看起来我们需要另一个坐标，一个智能合约的标识符。姑且称之为**自我**。

如果有可能不仅调用我们的智能契约的默认函数 *transfer* 而且还引入其他函数，会不会很方便？我们将如何解决这些问题？使用另一个属性坐标— **键**，它代表函数名。

现在，让我们在新的区块链上创建一个事务，它将一些令牌从地址 **orig** 传输到 **dest** 。

```
{
 "orig": "origin address",
 "dest": "destination address",
 "value": 100,
 "self": "smart contract address",
 "key": "transfer"
}
```

或者，创建新智能合约的交易:

```
{
 "orig": "origin address",
 "dest": "self — destination address",
 "value": "smart contract code"
}
```

现在我们有了一个简单的心智模型，允许我们使用无副作用的函数式编程方法轻松实现智能合同引擎。

更多代码示例和工作原型可以在[https://github.com/slavasn/bloqly](https://github.com/slavasn/bloqly)上找到