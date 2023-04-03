# 有效的智能合约测试:开发者回复评论

> 原文：<https://medium.com/coinmonks/effective-smart-contract-testing-developer-revert-comments-c7a6f250df0f?source=collection_archive---------3----------------------->

## 气体效率，有针对性的恢复测试？如何让你的[布朗尼](https://github.com/iamdefinitelyahuman/brownie)也能吃下去！

![](img/03654e2db806e11459a75e25b3726ac6.png)

测试智能契约的一个好方法是为函数的每个分支编写一个测试。考虑下面的例子:

测试`adminTransfer`时要考虑四个分支:

1.  成功转移，核实余额已正确调整。
2.  因为呼叫者不是所有者而失败的转移。
3.  由于接收方不被允许而失败的一种传输。
4.  因汇款人余额不足而失败的转账。

对于给定的代码，对失败路径的测试效果有限。例如，考虑以下测试:

测试通过了，但是我们不能确定由于余额不足而发生了回复。有可能`accounts[2]`不被允许接收令牌，我们有一个错误行为产生正确结果的例子。现在，假设我们后来对契约执行了重构，并且意外地删除了对足够余额的检查。因为我们实际上并没有测试我们认为正在测试的东西，所以这个测试仍然通过了，并且我们在代码中留下了一个相当大的未被发现的 bug。

# 恢复字符串救援！

幸运的是，有一个解决方案:我们可以在 require 语句中添加错误字符串，并将它们包含在测试中。以下是我们更新后的代码:

现在，我们更新我们的测试，专门针对预期的错误字符串:

现在，我们测试通过的唯一方法是，如果对`adminTransfer`的调用返回“余额不足”错误字符串。重构吧，朋友们，我们的测试用例是可靠的。

不幸的是，使用错误字符串是有代价的。**每一个错误字符串都会给合同部署成本**增加至少 20，000 gas，并且在执行该功能时会增加交易成本。由于[契约大小的限制](https://github.com/ethereum/EIPs/issues/170)，为每个`require`和`revert`语句包含一个错误字符串通常是不切实际的，有时甚至是不可能的。

所以我们面临一个艰难的决定。更好的测试用例，还是更高效的代码？

# 开发者回复评论拯救世界！

好消息是:我们实际上不需要做这个决定😄 [Brownie](https://github.com/iamdefinitelyahuman/brownie) 允许我们将恢复字符串作为源代码注释！这些错误字符串不包含在编译的字节码中，但是在运行测试时仍然可以访问。这是两全其美的方法— **您可以编写针对特定** `**require**` **或** `**revert**` **语句的测试，而不会影响您的契约**的效率。

查看我们的示例代码，更新后可以利用开发人员回复注释:

和更新的测试用例:

使用开发人员回复注释很简单。在包含潜在恢复的一行代码的末尾，添加一个以 Solidity 中的`// dev:`或 Vyper 中的`# dev:`开头的注释。布朗尼检测到这些评论，然后自动完成剩下的*。就这么简单！*

# *要了解更多信息…*

*查看 Brownie [文档](https://eth-brownie.readthedocs.io/en/stable/index.html)以了解更多关于[developer revert comments](https://eth-brownie.readthedocs.io/en/stable/tests-pytest-intro.html#developer-revert-comments)的信息，或者更一般的关于[在 Python 中测试智能合约](https://eth-brownie.readthedocs.io/en/stable/tests-pytest-intro.html)的信息。*

*你也可以加入 Brownie Gitter 频道，向其他使用 Brownie 进行测试的开发者提问或交流。并且，一如既往，随时可以通过[电子邮件](mailto:ben@hauser.id)或[电报](https://t.me/iamdefinitelyahuman)直接联系我。我很想收到你的来信。*

> *[直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)*

*[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)**[![](img/e9dbce386c4f90837b5db529a4c87766.png)](https://coincodecap.com)*