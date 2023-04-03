# 测试以太坊智能合约中的时间相关逻辑

> 原文：<https://medium.com/coinmonks/testing-time-dependent-logic-in-ethereum-smart-contracts-1b24845c7f72?source=collection_archive---------1----------------------->

![](img/b823d64f9618320eb42edb986d8d24fd.png)

A mechanical watch is a more dependable measure of time than a blockchain timestamp, so long as I keep wearing it.

写一个可靠的合同，只允许在特定的时间限制内执行特定的动作，这种情况并不少见。这方面的流行例子包括[open zeppelin](https://github.com/OpenZeppelin/zeppelin-solidity)合同。

当测试建立在`TimedCrowdsale`之上的合同时，你可能会遇到这样的情况，你测试的行为(比如，退款的分配)只有在众筹有`closed`时才能被测试。

该合同具有以下功能:

```
function hasClosed() public view returns (bool) {
    return now > closingTime;
}
```

这将检查`now`的值，并将其与合同的`closingTime`时间戳进行比较。

OpenZeppelin 还提供了一个非常常用的`FinalizableCrowdsale`契约，它扩展了`TimedCrowdsale`。通常，要实现这一点，您需要覆盖从`finalize`函数调用的`finalization`函数，如下所示:

```
function finalize() onlyOwner public {
    require(!isFinalized);
    require(hasClosed());
    finalization();
    Finalized();
    isFinalized = true;
}
```

为了对您自己的`finalization`函数进行单元测试，您需要确保`hasClosed()`返回 true。这意味着你需要弯曲时间。

> 还念:最好的[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)

# 时间是什么？

大多数编程环境都有时间的概念。例如，在 Javascript 中，您总是可以通过`const now = Date.now()`获得当前时间。这会根据执行脚本的计算机中的时钟，将当前的 *Unix 纪元*时间([自 1970 年 1 月 1 日](https://www.epochconverter.com)以来的毫秒数)分配给变量`now`。

然而，在以太坊智能合约中，时间的处理方式略有不同。在 Solidity 语言中，`now`实际上是对当前块的`timestamp`的简写引用，这是从 *Unix 纪元*以来的秒数，该函数的事务被记录到区块链。因此，该时间完全取决于挖掘该特定块的机器中的时钟。

一般来说，矿工需要一个精确的时钟。如果你的时钟与其他矿工的时钟不同步超过 12 秒，那么[你将很难连接到同伴](http://www.ethdocs.org/en/latest/network/connecting-to-the-network.html#common-problems-with-connectivity)。矿工操纵时间是值得警惕的事情。通常不鼓励依赖高度精确的时间戳的逻辑。

以太坊虚拟机只要求一个块的时间戳大于引用的前一个块的时间戳。在最初的以太坊白皮书中提到将时间戳限制为 15 分钟，但是这个信息已经过时了。最新的文件《以太坊黄皮书》取消了 15 分钟的限制。

> 参见[https://github.com/ethereum/yellowpaper](https://github.com/ethereum/yellowpaper)中的块标题有效性(第 4.4.4 节，等式 48)

然而，在大多数情况下，如果时间戳相差几分钟，这并不是一个大问题。

# 测试时间。

[Truffle](http://truffleframework.com) 允许你使用标准`[mochajs](https://mochajs.org)`单元测试框架的改编版本来编写智能合约的测试。您[可以用 Javascript](/level-k/testing-smart-contracts-with-truffle-7849b3d9961) 编写这些测试，并利用 *Mocha* 提供的所有模式，例如`before`块，并将测试分解成`contexts`。

比方说，我在一个合同中实现了自己的`finalization`功能，通过添加一个`overpaymentVault`来扩展`FinalizableCrowdsale`，这是一个特殊的钱包，可以存放人们发送的超出 indivudual 限额的邮件。一旦众筹上线，我们希望允许人们提取他们多付的钱，所以我们如下增强了`finalization`功能:

```
function finalization() internal {
    overpaymentVault.enableRefunds();
    super.finalization();
}
```

并覆盖标准的`claimRefund`功能:

```
function claimRefund() public {
    require(isFinalized);
    if (!goalReached() && vault.deposited(msg.sender) != 0) {
        vault.refund(msg.sender);
    }
    if (overpaymentVault.deposited(msg.sender) != 0) {
        overpaymentVault.refund(msg.sender);
    }
}
```

为了测试，我们需要能够完成合同。因为`finalization`是内部函数；我们不能直接从单元测试中测试它。因此，为了测试`claimRefund`，我们需要首先调用`finalize`，这意味着众筹需要`hasClosed()`返回`true`。

# 把时钟往前拨。

为了在测试中向前调时钟，我编写了一个名为`timeTravel`的小助手工具，它通过向区块链发送`evm_increaseTime`指令来指示矿工向前调时钟。这*不是*标准的 EVM 指令，而是添加到 [Ganache](https://github.com/trufflesuite/ganache-cli) 中的东西，Ganache 被 *Truffle* 内部用作测试区块链。

> `evm_increaseTime`:时间向前跳跃。接受一个参数，即以秒为单位增加的时间量。返回总时间调整，以秒为单位。

*Ganache* 还提供了另一个非标准指令`evm_mine`，简单的强制开采一个区块。

结合起来，这些指令可以使用`web3`发送，以迫使区块链在时间上向前行进。

```
const jsonrpc = '2.0'const id = 0const send = (method, params = []) =>
  web3.currentProvider.send({ id, jsonrpc, method, params })const timeTravel = async seconds => {
  await send('evm_increaseTime', [seconds])
  await send('evm_mine')
}module.exports = timeTravel
```

我可以这样使用它:

```
contract('MyCrowdsale', accounts => {
  const [
    wallet,
    overpaymentWallet,
    punter
  ] = accounts.slice(1) let crowdsale
  let token
  let refunded openingTime = Math.floor(new Date().getTime() / 1000)
  closingTime = openingTime + SECONDS_IN_A_DAY const rate = toWei(0.5)
  const amount = toWei(1.2)
  const expectedMax = amount.minus(rate.times(2))
  const goal = toWei(1) before(async () => {
    token = await MintableToken.new()
    crowdsale = await MyCrowdsale.new(
      openingTime,
      closingTime,
      wallet,
      overpaymentWallet,
      token.address,
      rate,
      goal
    ) await crowdsale.buyTokens(
      punter, { value: amount, from: punter })
 **await timeTravel(SECONDS_IN_A_DAY * 2)**    await crowdsale.finalize()
    const balance = web3.eth.getBalance(punter)
    await crowdsale.claimRefund({ from: punter })
    const newBalance = web3.eth.getBalance(punter)
    refunded = newBalance.minus(balance)
  }) it('claimRefund refunds the overpayment', () => {
    // not exact as gas costs can vary slightly
    assert.isTrue(refunded.gt(0) && refunded.lt(expectedMax))
  })
})
```

# 但是你回不去了！

*时间行程*测试**必须最后运行** **。一旦你把钟往前拨，你就不能再往回拨了。这是有问题的，因为您无法预测测试运行的顺序。测试确实在单个`contract`块中以可预测的顺序运行，但是一旦你开始将你的测试分成多个文件，使用它们自己的`contract`块，你就失去了预测顺序的能力。这意味着您的测试可能会以不可预知的方式中断。**

# 测试时间，第二次。

事实证明，对大多数合同来说，调整区块链的时钟是多余的。一种更简单的方法是用包含如下函数的高阶模拟契约来扩展您的智能契约:

```
contract MockMyCrowdsale is MyCrowdsale {
    function turnBackTime(uint256 secs) external {
        openingTime -= secs;
        closingTime -= secs;
    }
}
```

然后在上面的测试中，调用`MockMyCrowdsale.new(...)`而不是调用:

```
**await timeTravel(SECONDS_IN_A_DAY * 2)**
```

您可以调用:

```
**await crowdsale.turnBackTime(SECONDS_IN_A_DAY * 2)**
```

现在，该测试不会破坏您的其他测试的区块链。

经过多次试验，我还没有找到一个例子，你实际上需要操纵区块链自己的时间，而不是仅仅覆盖契约自己的特定时间值。

# 结论

通过利用 *Ganache* 以太坊测试网在 *Truffle* 框架内提供的几个非标准 EVM 指令，有可能将时间向前滚动，以便测试依赖于时间的逻辑，但这使您的测试非常脆弱。相反，最好使用模拟契约来扩展您正在测试的契约，模拟契约提供了一些函数，您可以使用这些函数来强制内部时间变量保存您需要的任何值。

## 链接

*   [松露框架](http://truffleframework.com) [Ganache](https://github.com/trufflesuite/ganache-cli)
    [用松露测试智能合约](/level-k/testing-smart-contracts-with-truffle-7849b3d9961)
*   [以太坊黄皮书](https://github.com/ethereum/yellowpaper)
*   OpenZeppelin 的 [Zeppelin-Solidity](https://github.com/OpenZeppelin/zeppelin-solidity) 合同。

像这样但不是订户？你可以通过[davesag.medium.com](https://davesag.medium.com/membership)加入来支持作者。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [Pionex 双重投资](https://coincodecap.com/pionex-dual-investment) | [AdvCash 审查](https://coincodecap.com/advcash-review) | [支持审查](https://coincodecap.com/uphold-review)
*   [面向开发者的 8 个最佳加密货币 APIs】](https://coincodecap.com/best-cryptocurrency-apis)
*   [拥护卡审核](https://coincodecap.com/uphold-card-review) | [信任钱包 vs MetaMask](https://coincodecap.com/trust-wallet-vs-metamask)
*   [赢取注册奖金——10 大最佳加密平台](https://coincodecap.com/earn-sign-up-bonus)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   [面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   杠杆代币的终极指南