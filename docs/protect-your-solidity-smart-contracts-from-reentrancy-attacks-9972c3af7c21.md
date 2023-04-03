# 保护您的可靠性智能合同免受重入攻击

> 原文：<https://medium.com/coinmonks/protect-your-solidity-smart-contracts-from-reentrancy-attacks-9972c3af7c21?source=collection_archive---------0----------------------->

![](img/5b6e31806de1c8e0e8c4d3cc1849057b.png)

在使用 Solidity 开发智能契约时，您需要警惕的最具破坏性的攻击之一是重入攻击。它们之所以具有破坏性，有两个原因:它们可以完全耗尽你的智能契约的以太，如果你不小心的话，它们可以潜入你的代码。

当您创建一个函数，在它解决任何影响之前对另一个不受信任的契约进行外部调用时，就可能发生可重入攻击。如果攻击者可以控制不受信任的协定，他们就可以递归调用原始函数，重复原本在解决影响后不会运行的交互。

这个最简单的例子是当一个契约使用一个余额变量进行内部核算并公开一个取款函数时。如果易受攻击的合同在其将余额设置为零之前转移资金，攻击者可以重复递归调用取款函数，并耗尽整个合同。

让我们看一个例子:

```
function withdraw() external {
    uint256 amount = balances[msg.sender];
    require(msg.sender.call.value(amount)());
    balances[msg.sender] = 0;
}
```

攻击者利用这个函数所需要的只是将一些余额映射到他们的智能契约地址，并创建一个调用 withdraw 的后备函数。

在`msg.sender.call.value(amount)()`转移正确数量的资金后，攻击者的回退函数再次调用`withdraw`，在`balances[msg.sender] = 0`可以停止进一步转移之前转移更多的资金。这种情况会持续下去，直到没有剩余的以太网，或者执行达到最大堆栈大小。

通常，易受攻击的函数会使用`transfer`、`send`或`call`进行外部调用。我们将在防止重入攻击一节中讨论这些函数之间的区别。

# 重入攻击的类型

重入攻击主要有两种类型:单函数重入和跨函数重入。

## **单函数重入攻击**

这种类型的攻击是最简单、最容易防范的。当易受攻击的函数与攻击者试图递归调用的函数相同时，就会发生这种情况。

我们前面的代码示例是一个单函数重入攻击。

## **跨函数重入攻击**

这些攻击更难检测。当易受攻击的函数与另一个对攻击者有预期效果的函数共享状态时，就可能发生跨函数可重入攻击。

这最容易用一个例子来解释:

```
function transfer(address to, uint amount) external {
    if (balances[msg.sender] >= amount) {
        balances[to] += amount;
        balances[msg.sender] -= amount;
    }
}function withdraw() external {
    uint256 amount = balances[msg.sender];
    require(msg.sender.call.value(amount)());
    balances[msg.sender] = 0;
}
```

在本例中，`withdraw`调用攻击者的回退函数，与单函数重入攻击相同。

不同之处在于回退函数调用`transfer`而不是递归调用`withdraw`。因为在这个调用之前余额没有被设置为 0，`transfer`函数可以转移一个已经花掉的余额。

# 可重入攻击能有多糟糕？

问问 2016 年投资道的人就知道了。DAO 黑客攻击是以太坊历史上最引人注目的重入攻击之一。一个攻击者设法从它身上吸走了大约 360 万乙醚。

DAO 有一个易受攻击的函数，该函数意在分裂出一个子 DAO。攻击者使用这个函数递归地将资金从原始 DAO 转移到他们控制的子 DAO。

黑客的破坏如此严重，以太坊基金会不得不求助于一个有争议的硬分叉来收回投资者的资金。大多数人支持 hard fork，但社区的一部分人认为它违反了加密货币的核心原则——即不变性——并继续使用旧链，导致了以太坊经典的创建。

在这里阅读更多关于 DAO 黑客的信息:[http://hacking distributed . com/2016/06/18/analysis-of-the-DAO-exploit/](http://hackingdistributed.com/2016/06/18/analysis-of-the-dao-exploit/)

# 防止重入攻击

您应该遵循一些最佳实践来保护您的智能契约免受重入攻击。

## 发送、转移和呼叫

因为大多数重入攻击涉及`send`、`transfer`或`call`函数，所以理解它们之间的区别很重要。

`send`和`transfer`功能被认为更安全，因为它们被限制在 2,300 gas。gas 限制防止了昂贵的外部函数回调目标协定。一个陷阱是当合同使用`msg.sender.call(ethAmount).gas(gasAmount)`为发送或转移设置自定义的气体量时。

不幸的是,`call`函数更容易受到攻击。

当外部函数调用预计执行复杂操作时，您通常希望使用`call`函数，因为它会转发所有剩余气体。这为攻击者在单个函数重入攻击中调用原始函数，或者在跨函数重入攻击中调用不同于原始契约的函数打开了方便之门。

尽可能使用`send`或`transfer`代替`call`来限制您的安全风险。

## **标记不可信函数**

为了防止重入攻击，识别函数何时不受信任是很重要的。Consensys 最佳实践建议您命名函数和变量，以表明它们是否不受信任。

例如:

```
function untrustedWithdraw() public {
    uint256 amount = balances[msg.sender];
    require(msg.sender.call.value(amount)());
    balances[msg.sender] = 0;
}
```

重要的是要记住，如果一个函数调用另一个不可信的函数，它也是不可信的。

```
function untrustedSettleBalance() external {
    untrustedWithdraw();
}
```

## **检查-效果-交互** **模式**

防止重入攻击的最可靠的方法是使用 checks-effects-interactions 模式。

这种模式定义了构建函数的顺序。

首先在函数开始时执行任何检查，通常是`assert`和`require`语句。

如果检查通过，那么该函数应该解决对契约状态的所有影响。

只有在解决了所有状态更改之后，该函数才应该与其他契约进行交互。通过最后调用外部函数，即使攻击者对原始函数进行递归调用，他们也无法滥用契约状态。

让我们使用 checks-effects-interactions 模式重写易受攻击的取款函数。

```
function withdraw() external {
    uint256 amount = balances[msg.sender];
    balances[msg.sender] = 0;
    require(msg.sender.call.value(amount)());
}
```

因为我们在进行外部调用之前将余额(一种效果)清零，所以攻击者进行的递归调用不会被欺骗而认为还有余额。

## **互斥**

在更复杂的情况下，比如防止跨函数重入攻击，可能有必要使用互斥。

互斥锁锁定契约状态。只有锁的所有者可以修改状态。

让我们看一个简单的互斥实现。

```
function transfer(address to, uint amount) external {
    require(!lock);
    lock = true; if (balances[msg.sender] >= amount) {
        balances[to] += amount;
        balances[msg.sender] -= amount;
    } lock = false;
}function withdraw() external {
    require(!lock);
    lock = true; uint256 amount = balances[msg.sender];
    require(msg.sender.call.value(amount)());
    balances[msg.sender] = 0; lock = false;
}
```

通过使用这个锁，攻击者不能再通过递归调用来利用`withdraw`函数。攻击者也不能利用对`transfer`的调用进行跨函数重入攻击。当`lock`为`true`时，所有状态修改发生，防止任何检查锁的功能被无序调用。

您必须小心实现互斥体，以确保总是有释放锁的方法。如果攻击者可以锁定您的合同并阻止它的发布，您的合同就会失效。

OpenZeppelin 有自己的互斥实现，您可以使用它，名为`ReentrancyGuard`。这个库提供了一个修饰符，你可以把它应用到任何一个叫做`nonReentrant`的函数上，这个函数用一个互斥体来保护这个函数。

在这里查看 OpenZeppelin `ReentrancyGuard`库的源代码:[https://github . com/open zeppelin/open zeppelin-solidity/blob/master/contracts/utils/reentrancy guard . sol](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/utils/ReentrancyGuard.sol)

请记住，`nonReentrant`函数应该是外部的。如果另一个函数调用`nonReentrant`函数，它将不再受保护。

# 可重入攻击的未来

未来的更新总是有引入更多攻击机会的风险。君士坦丁堡的更新被推迟了，因为在 EIP 1283 中发现了一个缺陷，该缺陷使用某些 T3 操作引入了一种新的重入攻击。如果将此更新部署到 mainnet，甚至发送和传输功能也会受到攻击。

攻击将变得越来越高级，并涉及功能和契约之间更复杂的交互来影响状态。为了保持领先地位，我们能做的最好的事情就是保持交互尽可能简单，并采用最佳实践，例如使用 transfer 或 send 而不是 call，并使用 checks-effects-interactions 模式来构建我们的功能。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   开发人员的最佳加密 API
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   杠杆代币的终极指南
*   [加密交易的最佳 VPNs】](https://coincodecap.com/best-vpns-for-crypto-trading)
*   [火币加密交易信号](https://coincodecap.com/huobi-crypto-trading-signals) | [HitBTC 审核](/coinmonks/hitbtc-review-c5143c5d53c2)
*   [TraderWagon 回顾](https://coincodecap.com/traderwagon-review) | [北海巨妖 vs 双子 vs 比特亚德](https://coincodecap.com/kraken-vs-gemini-vs-bityard)
*   [如何在 FTX 交易所交易期货](https://coincodecap.com/ftx-futures-trading)
*   [OKEx vs KuCoin](https://coincodecap.com/okex-kucoin) | [摄氏替代品](https://coincodecap.com/celsius-alternatives) | [如何购买 VeChain](https://coincodecap.com/buy-vechain)
*   [3 commas vs . Pionex vs . crypto hopper](https://coincodecap.com/3commas-vs-pionex-vs-cryptohopper)
*   [如何使用 Cornix 交易机器人](https://coincodecap.com/cornix-trading-bot)
*   [Bitget 回顾](https://coincodecap.com/bitget-review)|[Gemini vs block fi](https://coincodecap.com/gemini-vs-blockfi)cmd |[OKEx 期货交易](https://coincodecap.com/okex-futures-trading)
*   [用信用卡购买密码的 10 个最佳地点](https://coincodecap.com/buy-crypto-with-credit-card)