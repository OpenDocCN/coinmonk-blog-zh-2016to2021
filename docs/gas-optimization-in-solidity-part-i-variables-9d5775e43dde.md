# 固体中的气体优化第一部分:变量

> 原文：<https://medium.com/coinmonks/gas-optimization-in-solidity-part-i-variables-9d5775e43dde?source=collection_archive---------1----------------------->

![](img/5fe3ef68e1028c2ff0943a039b75c7d9.png)

*本文是为 Solidity 0.5.8* 编写的

Gas 优化是开发以太坊智能合约所特有的挑战。为了成功，我们需要学习 Solidity 如何处理我们的变量和函数。

因此，我们将气体优化分为两部分。

在第一部分中，我们通过学习变量打包和数据类型权衡来讨论变量。

在第二部分中，我们通过学习可见性、减少执行和减少字节码来讨论函数。

我们讨论的一些技术会违反众所周知的代码模式。在优化之前，我们应该始终考虑我们可能产生的技术债务和维护成本。

# 优化变量

## 可变包装

可靠性契约具有用于存储的连续 32 字节(256 位)槽。当我们把变量排列成多个放在一个槽中时，我们称之为变量打包。

可变包装就像一个俄罗斯方块游戏。如果我们试图打包的变量超过了当前槽的 32 字节限制，它将被存储在一个新的槽中。我们必须找出哪些变量最适合组合在一起，以尽量减少浪费的空间。

因为每个储气槽都要消耗汽油，可变包装通过减少合同要求的储气槽数量，帮助我们优化汽油使用。

让我们看一个例子:

```
uint128 a;
uint256 b;
uint128 c;
```

这些变量没有打包。如果`b`与`a`打包在一起，它将超过 32 字节的限制，因此它将被放入一个新的存储槽中。同样的事情也发生在`c`和`b`身上。

```
uint128 a;
uint128 c;
uint256 b;
```

这些变量是打包的。因为用`a`打包`c`没有超过 32 字节的限制，所以它们被存储在同一个槽中。

选择数据类型时要记住变量打包——数据类型的较小版本只有在有助于将变量打包到存储槽中时才有用。如果 a `uint128`装不下，我们还不如用一个`uint256`。

**数据位置**试图打包函数参数或局部变量不会节省空间。

**引用数据类型**
结构和数组总是从一个新的存储槽开始——但是它们的内容可以正常打包。一个`uint8`数组将比一个等长的`uint256`数组占用更少的空间。

用单独的赋值来初始化紧密封装的结构比用单个赋值更有效。单独的赋值使得优化器更容易一次更新所有的变量。

像这样初始化结构:

```
Point storage p = Point()
p.x = 0;
p.y = 0;
```

而不是:

```
Point storage p = Point(0, 0);
```

**继承**

变量的顺序由 [C3 线性化](https://en.wikipedia.org/wiki/C3_linearization)决定。对于大多数应用程序，你只需要知道子变量在父变量之后。

## 数据类型

当选择数据类型来优化 gas 时，我们必须管理权衡。不同的情况会使相同的数据类型变得便宜或昂贵。

**内存与存储**
在内存上执行操作——或者调用数据，类似于内存——总是比存储便宜。

减少存储操作数量的一种常见方法是在将局部内存变量分配给存储变量之前对其进行操作。

我们经常在循环中看到这种情况:

```
uint256 return = 5; // assume 2 decimal places
uint256 totalReturn;function updateTotalReturn(uint256 timesteps) external {
    uint256 r = totalReturn || 1; for (uint256 i = 0; i < timesteps; i++) {
        r = r * return;
    } totalReturn = r;
}
```

在`calculateReturn`中，我们使用本地内存变量`r`来存储中间值，并将最终值赋给我们的存储变量`totalReturn`。

**固定与动态**
固定大小的变量总是比动态的便宜。

如果我们知道数组应该有多长，我们指定一个固定的大小:

```
uint256[12] monthlyTransfers;
```

同样的规则也适用于字符串。一个`string`或`bytes`变量被动态调整大小；如果我们的字符串足够短，我们应该使用一个`byte32`。

如果我们绝对需要一个动态数组，那么最好将我们的函数构造成可加的而不是次活跃的。扩展数组消耗的是常量气体，而截断数组消耗的是线性气体。

**映射 vs 数组**
大多数时候使用`mapping`比使用数组更好，因为它的操作成本更低。

但是，当使用较小的数据类型时，数组可能是正确的选择。数组元素像其他存储变量一样被打包，减少的存储空间可能会超过数组更昂贵的操作的成本。这在处理大型数组时非常有用。

## 其他技术

在处理变量时，还有其他一些技巧可以帮助我们优化天然气成本。

**初始化**
Solidity 中的每个变量赋值都要耗费 gas。在初始化变量时，我们经常浪费时间去指定永远不会用到的默认值。

`uint256 value;`比`uint256 value = 0;`便宜。

**Require strings**
如果我们要给 Require 语句添加消息字符串，我们可以通过将字符串长度限制为 32 个字节来降低成本。

**解包变量**
EVM 一次处理 32 个字节，小于 32 个字节的变量被转换。如果我们不通过打包变量来节省能源，那么使用 32 字节的数据类型会更便宜，比如`uint256`。

**删除**
以太坊在我们删除变量的时候给我们退气。其目的是鼓励节省区块链的空间，我们用它来降低交易的汽油成本。

删除一个变量可退还 15，000 美元的汽油费，最高可达交易汽油费的一半。用`delete`关键字删除相当于给数据类型赋初始值，比如用`0`给整数。

**将数据存储在事件中**
不需要链上访问的数据可以存储在事件中以节省气体。

虽然这种技术可行，但不推荐使用——事件并不意味着数据存储。如果我们需要的数据存储在很久以前发出的事件中，由于我们需要搜索的块的数量，检索它会非常耗时。

# 优化功能

*固体中的气体优化第二部分:功能*即将推出…

*如果这篇文章对您有帮助，请点击* ***或点击******❤***按钮，以便其他人可以找到它。**

## *在 [LinkedIn](https://www.linkedin.com/in/willshahda) 和 [Twitter](https://twitter.com/ethdapp) 上与我联系。*

> *加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)*

## *另外，阅读*

*   *[复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)*
*   *[网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)*
*   *[密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)*
*   *[最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)*
*   *[开发者最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)*
*   *最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)*
*   *[杠杆代币](/coinmonks/leveraged-token-3f5257808b22)终极指南*