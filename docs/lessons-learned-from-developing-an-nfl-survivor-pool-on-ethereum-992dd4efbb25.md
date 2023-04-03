# 以太坊上开发 NFL 幸存者池的经验教训

> 原文：<https://medium.com/coinmonks/lessons-learned-from-developing-an-nfl-survivor-pool-on-ethereum-992dd4efbb25?source=collection_archive---------5----------------------->

我最近开始在以太坊上开发一个 NFL 幸存者应用程序。对于那些不熟悉幸存者池的人来说，基本游戏规则如下:

*   支付所需的报名费加入幸存者池
*   每周选择一个你认为会赢的队。如果你的队赢了，你进入下一周。如果你的队输了，你就出局了。
*   每个团队只能被挑选一次
*   最后站着的人赢得奖金
*   如果所有剩下的球员被一次淘汰，或者当 NFL 赛季结束时还有多名球员，奖金将在他们之间平均分配

![](img/d1c3953f2dcd17a666665f06e63b3697.png)

[Source](https://bitedge.com/blog/nitrogen-sports-nfl-survivor-pool/)

这比我最初预期的要复杂一些。以下是我在开发过程中面临的各种挑战以及我是如何应对的。

# 获得游戏结果

为了确定每周之后哪些球员留下来，我们需要知道哪些球队赢得了他们的 NFL 比赛，然后我们需要将这些获胜者与每个球员的选择进行比较。以不可信的方式将外部数据引入区块链是一个棘手的问题，尤其是当您需要将这些数据与每个加入的玩家进行比较时。

## 使用 Oraclize 的不可信方法

一个相当不可信的解决方案是使用 [Oraclize](http://www.oraclize.it/) (你确实需要信任 Oraclize)。我们可以通过两种方式使用该服务来确定 NFL 比赛的获胜者:

1.  使用一个标准的 API 调用，在我之前写的[教程](/coinmonks/using-apis-in-your-ethereum-smart-contract-with-oraclize-95656434292e)中有详细描述
2.  使用[计算数据源](http://docs.oraclize.it/#data-sources-computation)不仅可以获得游戏赢家，还可以决定哪些玩家留下，哪些被淘汰。

在最坏的情况下，我们总共需要联系甲骨文 17 次(在 NFL 赛季的 17 周中，每周一次)。使用选项 1，假设我们可以在一个 API 调用中获得所有游戏结果，Oraclize 默认 gas 参数将花费我们每周 0.004 ETH 或整个赛季 0.068 ETH。这意味着，在目前约 300 美元/ETH 的价格水平下，该合同将在整个赛季中花费约 20 美元来确定获胜者。这是在之前的*增加了字符串解析和操作的 gas 成本(这在可靠性上是复杂和昂贵的),以及确定剩余玩家所需的循环。由于解析结果和循环所有玩家以确定剩余玩家的成本和复杂性，所以排除了选项 1。*

让我们看看选项 2。Oraclize 计算数据源要求脚本将计算结果打印为最后一行。这意味着我们可以:

1.  打印一串获胜(或失败)的团队，并在 Solidity 中解析它们，或者
2.  使用一个更进一步的计算，打印出一个字符串，告诉我们还有哪些玩家

方法 1 仍然引入了上文讨论的字符串操作和循环的复杂性，如果可能的话，我们希望避免这种复杂性，所以这不是一个好的选择。

为了澄清方法 2 所建议的:假设我们有一个名为`playersEntered`的地址数组，它存储每个进入的玩家的地址，还有一个名为`isNotEliminated`的布尔数组，它存储每个玩家是否被淘汰。然后我们可以让脚本返回一个字符串，比如`'1001110'`，其中第一个数字表示`players[0]`还活着，但是`players[1]`被消灭了。这种方法和上面的所有其他方法一样，需要我们遍历每个参与者来适当地更新契约的状态。随着玩家数量的增加，这样做的成本会变得很高，因此也是不利的。

## 更加信任的方法

在信任、成本和复杂性之间有一个权衡，这是一个可以探索的问题。

对于所有玩家来说，如果他们因为为了合同所有者的利益而使用虚假的比赛结果而被错误地淘汰，这将是非常明显的。因此，另一种方法是，在我们自己的服务器上，调用 API 来获取 NFL 获胜者，然后确定我们自己服务器上的剩余球员。接下来，仍然使用我们的服务器，我们可以从我们的服务器调用一个合同函数，并提供一个剩余玩家的数组。我们可以用 API 提供的变量来更新保存玩家状态的契约变量，这样就不需要任何循环了！

然而，即使很明显存在操纵行为，我们目前也没有为玩家提供一种方式来要回他们的钱。如果你想增加更多的信任，一个办法是使用某种类型的投票系统。实现一个用户调用的函数，如果他们认为他们被错误地淘汰了，如果超过 X%的用户调用这个函数，所有的用户资金都被返回。这里的风险在赛季后期显现出来，那时只有少数球员留下。什么能阻止所有失败的玩家调用这个函数来要回他们的钱，不管结果是否是伪造的？

就目前而言，我选择使用本节中描述的方法，因为这种方法简单且具有成本效益，尽管我不喜欢它需要信任合同所有者自己的 API 来确定剩余的参与者。这是我想在本合同的未来版本中更新的内容。

# 计划未来交易

每周，需要调用各种合同函数来做一些事情，比如从 API 获取游戏结果，并更新本周的选择截止日期。理想情况下，这些函数将被自动调用，因此合同部署人员不需要每周手动调用它们。我们如何做到这一点？

一个有趣的解决方案是使用[以太网闹钟](https://www.ethereum-alarm-clock.com/)，但是不幸的是它还没有在 mainnet 上部署(尽管它已经在 Ropsten 和 Kovan testnets 上部署)。它还依赖于激励人类进行函数调用，这意味着您将无法在专用网络上测试这种方法。

或者，如果您使用 [Geth](https://geth.ethereum.org/) 或[奇偶校验](https://www.parity.io/)运行节点，您可以通过您的节点调度事务。[这个 StackExchange 问题的答案](https://ethereum.stackexchange.com/questions/16464/how-to-schedule-a-transaction)解释了您如何做到这一点。然而，我不运行一个节点，也不想设置一个，所以这个选项是不可行的。另一个缺点是，在通读合同时没有明确说明这个功能是如何实现的。

第三种方法是激励玩家为我们调用所需的函数。我们可以通过向他们支付少量款项来实现这一点，作为调用该函数的奖励。显而易见的缺点是，仍然不能保证这个函数会被调用，尤其是如果报酬不够高的话。如果联邦医疗保险/美元价格发生变化，回报可能会变得不那么诱人，或者太高。此外，添加另一个用户可以调用的函数会增加合同的攻击面。

我所知道的下一个方法是使用[或](http://www.oraclize.it/)，无论是否有 API 调用。这不是一个标准的 Oraclize 用例，所以让我们在这里深入一点。使用 Oraclize 时，典型的执行流程如下所示:

1.  手动调用合同中的某个函数`foo()`
2.  `foo()`以应用编程接口查询作为`oraclize_query()`的输入之一调用`oraclize_query()`
3.  Oraclize 到达指定的 API 并等待响应
4.  Oraclize 调用您的合同中的一个名为`__callback()`的函数，并将查询结果传递给它

“技巧”来自步骤 2，其中`oraclize_query()`实际上允许您传入一个[延迟参数](http://docs.oraclize.it/#ethereum-quick-start-schedule-a-query-in-the-future)，它将 API 调用推迟指定的时间量。如果您以空字符串的形式输入 API 查询输入，Oraclize 将不会抛出错误，并且现在将充当事务调度程序。简单例子见 [this StackExchange](https://ethereum.stackexchange.com/a/252/44133) 答案。

Oraclize 方法的另一个好处是，它可以很容易地用于[调度递归查询](http://docs.oraclize.it/#ethereum-quick-start-recursive-queries)，使得每周重复我们的函数调用变得很容易。如上所述，这种方法的缺点是成本很高。由于它易于实现，而且我已经从以前的[教程](/coinmonks/using-apis-in-your-ethereum-smart-contract-with-oraclize-95656434292e)中熟悉了 Oraclize，所以我决定降低成本，使用这种方法。使用上一节中的数字，Oraclize 查询的默认成本是 0.004 ETH，相当于每周 1.20 美元。

最后一个值得一提的选项是在您自己的服务器上建立一个 [cron 作业](https://en.wikipedia.org/wiki/Cron),根据需要从普通帐户调度函数调用。就像从一个节点调度事务一样，在阅读合同时并不清楚这是否实现了。

# 测试未来交易

通常情况下，智能合约功能只能在特定时间内调用。在这种情况下，我们必须阻止玩家在游戏开始后选择他们的团队。那么我们如何做到这一点呢？

我们将使用 [ganache-cli](https://github.com/trufflesuite/ganache-cli) 和 [Truffle](https://truffleframework.com/truffle) 编写我们的测试，并利用名为`evm_increaseTime`的 ganache 特性来改变时间。这种方法允许我们随意增加 EVM 时间，所以我们可以在将来模拟呼叫。

然而，这种方法似乎有一些问题，因为我不能让它正常工作。以下函数用于使用`evm_increaseTime`方法增加 EVM 时间:

然而，从该代码片段的注释中可以看出，这并不能正常工作。根据[这个](https://github.com/trufflesuite/ganache-cli/issues/394)问题，我们需要在更改时间后挖掘一个区块，以使时间更改真正生效。我们使用`evm_mine`方法来实现，如果我们检查 ganache-cli 时间戳，这看起来确实有效。然而，如果我们在我们的契约中添加一个简单返回`now`或`block.timestamp`的函数，我们会看到时间改变没有被遵守。类似地，依赖于`now`的测试会失败，因为合同不承认时间改变。下面的代码片段演示了这一点:

这里有一个关于这个[的未决问题，因此我无法测试未来的交易。这有点令人失望，因为这个合同相当依赖于在特定时间发生的事情。如果有人有关于如何让它正常工作的建议，我很乐意听听！](https://github.com/trufflesuite/ganache-cli/issues/336)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)