# 缺少返回值错误—至少 130 个令牌受到影响

> 原文：<https://medium.com/coinmonks/missing-return-value-bug-at-least-130-tokens-affected-d67bf08521ca?source=collection_archive---------0----------------------->

TL；dr 由于最近的 Solidity 更新，许多 ERC20 令牌合同中出现了一个严重的错误。

**ERC 基础知识**

ERC20 标准是以太坊平台上最常见的令牌标准。ERC20 被定义为一个接口，该接口指定了智能合约中必须实现哪些功能和事件才能符合 ERC20。目前，这些措施如下:

**ERC 20 合约的传递函数应该返回什么？**

在 ERC20 的开发过程中，曾就 ERC20 合约传递函数的正确返回值进行了长时间的讨论。在这次讨论中基本上有两个阵营。一方认为，如果传递不允许调用契约中的错误处理，那么传递函数应该返回 false。另一方声称，在无法确保安全的情况下，ERC20 应恢复交易。这个问题从未真正解决，这两种行为现在都被认为是符合 ERC20 的。最常见的行为是失败时的恢复，特别是因为 OpenZeppelin 通过这种方式实现了 ERC20 令牌。

所有这些与我想要描述的实际 bug 没有什么关系，但是可能有助于提供一些上下文。

**臭虫**

事实证明，很大一部分 ERC20 令牌在传递函数的返回值方面表现出另一种方式。

这些令牌契约的传递函数(姑且称之为 BadTokens)不返回任何东西。他们遵循的接口如下所示:

该接口不符合 ERC20 标准，这意味着实现该接口的契约不是 ERC20 令牌。

拥有令牌标准的一个要点是，使其他智能合约能够通过一个公共接口与许多不同的令牌合约进行交互。有许多不同的合同使用 ERC20 令牌的这一功能。分散交易所可以发送代币，众卖合约可以接受 ERC20 代币作为支付等等。现在有趣的问题是，如果一个期望 ERC20 接口的契约试图与一个不符合 ERC20 的 BadToken 交互，会发生什么？

让我们来看看这个场景:

在 Solidity 中，函数选择器是从它的函数名和输入参数的类型中派生出来的。

```
selector = bytes4(sha3(“transfer()”))
```

函数的返回值不是函数选择器的一部分。所以一个没有返回值的函数*transfer()**和一个函数*transfer()returns(bool)*有相同的函数选择器，但它们仍然不同。如果你愿意尝试*

```
*contract BadToken is Token {                      
   function transfer() {}         
}*
```

*由于缺少返回值，编译器不会接受 transfer()函数作为令牌接口的实现。所以 GoodToken 是 Token 接口的实现，BadToken 不是。*

*如果我们称它为“无论如何”,会发生什么？*

*调用契约向 BadToken 发送外部调用，bad token 处理调用，进行传输，并且不返回布尔返回值。调用契约现在在内存中查找返回值，但是由于令牌没有写入返回值，所以它会将在该内存位置找到的任何内容作为外部调用的返回值。*

*这已经很糟糕了:将一些恰好在内存槽中的数据作为返回值不是一个好主意。*

*纯属巧合，这个问题在过去没有出现，因为调用者期望返回值的内存槽与存储调用的函数选择器的内存槽重叠。这被 EVM 解释为返回值“true”。因此，由于运气好，EVM 表现得像程序员希望它表现的那样。*

*但对于较新的智能合约来说，情况并非如此。*

***什么变了？***

*自从去年 10 月拜占庭硬分叉之后，EVM 有了一个新的操作码，叫做 *RETURNDATASIZE。*这个操作码存储(顾名思义)外部调用返回数据的大小。这是一个非常有用的操作码，因为它允许在函数调用中返回动态大小的数组。*

*Solidity 0.4.22 更新中采用了这个操作码(当设置为默认的后拜占庭模式时)。现在，代码在外部调用后检查返回值的大小，并在返回数据比预期短时恢复事务。这比从某个内存插槽中读取数据要安全得多。但是这种新行为对我们的 BadTokens 来说是个大问题。*

***谁受影响。***

*为了了解这个问题有多普遍，我拿出了 Etherdelta 上列出的 ERC20 令牌列表，并检查了它们在 Etherscan 上验证的 API。因此，我找到了 130 个受影响的象征性合同。*

*这份名单上有一些名人:*

*名单上最大的标志(按市值计算)是:*

***币安币 1.587.146.847 美元
奥米塞戈币 1.127.641.627 美元***

*但是这个列表中也有很多中小型代币。*

***有什么风险？***

*如上所述，最大的风险是，用 solc ≥ 0.4.22 编译的智能合约(它需要一个 ERC20 接口)将无法与我们的 BadTokens 进行交互。这可能意味着发送到这种合同的令牌将永远卡在那里，即使该合同具有转移 ERC20 令牌的功能。在许多不同的场景中，处理 ERC20 令牌的契约都会遇到这个 bug。一个例子是，你将不能使用分散的交易所，它与 solc ≥ 0.4.22 的合同与你的 BadToken 一起编译。*

***怎么会这样？***

*这个问题显然是令牌合约中的一个 bug。坚固性的改变只是把这个错误暴露出来。有这么多 BadTokens 的一个原因是 OpenZeppelin 在他们的框架中实现了错误的接口。在 2017 年 3 月 17 日和 2017 年 7 月 13 日之间，接口出错:*

*[https://github . com/open zeppelin/open zeppelin-solidity/blob/52120 A8 c 428 de 5 e 34 f 157 b 7 ea ed 16d 38 f 3029 e 66/contracts/token/ERC 20 basic . sol](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/52120a8c428de5e34f157b7eaed16d38f3029e66/contracts/token/ERC20Basic.sol)*

*因此，大多数 BadTokens 使用了 ERC20 接口的有缺陷的 OpenZeppelin 实现。*

***那么现在怎么办？***

*有两种方法可以修复这个 bug，我认为这两种方法需要并行处理。*

*一方面，受影响的令牌合同的团队需要修改他们的合同。这可以通过重新部署令牌合约或通过更新合约来完成，如果令牌合约具有更新编程到其中的合约的某种能力的话。*

*另一种可能性是用一个调用坏的传递函数并返回好的传递函数的契约来包装令牌契约。对于这种包装器有不同的提议。例如*

*另一方面，正在编写处理 ERC20 令牌的合同的开发人员需要了解这个 bug，这样他们就可以预测 BadTokens 的意外行为并处理它们。这可以通过期待 BadERC20 接口并在调用后检查返回数据来确定我们调用的是 GoodToken 还是 BadToken 来实现。*

***结论***

*我认为目前没有理由恐慌，因为分散交易所和其他大合约的更新周期相当长。然而，这个错误需要尽快修复。*

*关于这个问题，还有一篇来自 [Christian](https://medium.com/u/8c4229f925b6?source=post_page-----d67bf08521ca--------------------------------) 的内容丰富的文章。[https://medium . com/@ Chris _ 77367/explaining-unexpected-revotes-starting-with-solidity-0-4-22-3 ada 6e 82308 c](/@chris_77367/explaining-unexpected-reverts-starting-with-solidity-0-4-22-3ada6e82308c)*

*你可以在 github 上看到这个问题:[https://github.com/ethereum/solidity/issues/4116](https://github.com/ethereum/solidity/issues/4116)*

*感谢克里斯蒂安、热拉尔和伊娃对本文的帮助*

***关于卢卡斯-柏林***

*我是一名驻柏林的 solidity 开发人员和审计员。我也是 sicos.io 的 CTO 和 Solidity Berlin meetup 的组织者。(www.solidity.berlin)*

> *加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)*

## *另外，阅读*

*   *[复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)*
*   *[网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)*
*   *[密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)*
*   *[最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)*
*   *开发人员的最佳加密 API*
*   *[NFT 十大市场造币集锦](https://coincodecap.com/nft-marketplaces)*
*   *[AscendEx Staking](https://coincodecap.com/ascendex-staking)|[Bot Ocean Review](https://coincodecap.com/bot-ocean-review)|[最佳比特币钱包](https://coincodecap.com/bitcoin-wallets-india)*
*   *[Bitget 评论](https://coincodecap.com/bitget-review)|[Gemini vs block fi](https://coincodecap.com/gemini-vs-blockfi)|[OKEx 期货交易](https://coincodecap.com/okex-futures-trading)*
*   *[美国最佳加密交易机器人](https://coincodecap.com/crypto-trading-bots-in-the-us) | [经常性评论](https://coincodecap.com/changelly-review)*
*   *[在印度利用加密套利赚取被动收入](https://coincodecap.com/crypto-arbitrage-in-india)*
*   *最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)*
*   *[免费加密信号](/coinmonks/free-crypto-signals-48b25e61a8da) | [加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)*
*   *杠杆代币的终极指南*