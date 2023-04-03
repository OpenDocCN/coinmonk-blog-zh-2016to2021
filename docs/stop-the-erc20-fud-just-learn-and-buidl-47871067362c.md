# 停止 ERC20 FUD。学习和#BUIDL

> 原文：<https://medium.com/coinmonks/stop-the-erc20-fud-just-learn-and-buidl-47871067362c?source=collection_archive---------1----------------------->

有一篇文章正在# ERC20 #以太坊社区中制造大量的#FUD。要知道“batchTransfer”甚至不是 ERC20 标准的一部分。这是关于如何避免这种错误的文章和我的回应。

![](img/16e273731671b8c4315ee38ad7a02094.png)

[https://medium . com/@ Dan . Ryan . Emmons/its-essentially-the-same-type-of-that-in-any-type-of-algorithm-since-3020 a 9893159](/@dan.ryan.emmons/its-essentially-the-same-type-of-overflow-that-can-occur-in-any-type-of-arithmetic-since-3020a9893159)

现在，我同意——但是[我已经做了 18 年的开发人员了](https://linkedin.com/in/dan-emmons),我可以告诉你，即使是我们当中最优秀的人，用了近 20 年编写高度可靠、高度安全的金融应用程序，也会犯一些小错误，导致灾难性的后果。过去，开发人员可以简单地停止服务器，恢复他们控制的数据库中的更改，并修补一个错误。但我们正处于一个新的范式中。我们部署到区块链的代码是不可变的。它无法修复(直到我们开发出更好的方法来创建可升级/代理合同)。我们可以利用这一时刻来学习和扩展我们的知识。

> 我们的知识增长得越多，我们的无知展现得越多。

我们所使用的语言和工具发展如此之快，以至于为了完成我们的任务，要跟上我们所使用的工具的 24/7/365 的变化几乎是一项全职工作。这是一项令人望而生畏的工作，如果你放任不管，它真的会让你精疲力尽。看看以太坊的这个[周，仅仅一周就发生了什么变化！](http://www.weekinethereum.com/post/173096001428/april-19-2018)

> 这样的速度在驱除旧疾的同时，也不可避免地产生新的病痛。因此，有些人希望我们在现在的地方多呆一会儿也就不足为奇了。休息，等待。

但现在不是讨论悲观理由的时候。使用#区块链/#加密技术，人类从未像现在这样强大。这是 30 年来最激动人心、最有力的创新。

> 如果说这段人类进步的简史教会了我们什么的话，那就是人类及其对知识的追求是坚定不移的。

当我们部署我们的智能合同时，这与向太空发射一枚不可阻挡的火箭非常相似，并希望所有的编码时间，在 Truffle 框架中使用 Mocha 进行测试的时间，所有致力于集成测试的时间，所有辛苦进行审计的时间——我们希望我们没有遗漏任何可能导致整个操作失败的微小错误。

但是#Blockchain 和#SmartContract 开发中的这些技术进步并不是由那些等待、休息并希望看到背后的人建立的。对这一领域的探索将会继续，无论你是否准备好与我们一起# BUIDL。那些在我们之前的人确信我们能够驾驭现代发明的第一波浪潮和赋权的第一波浪潮。

**这一代人不打算在即将到来的区块链时代的逆流中失败。*我们希望成为其中的一员。我们打算领导它。如果你想加入我们，请一起来学习#***[***how to guidl***](/coinmonks/howtobuidl-series-1-of-n-bf51e248243d)***系列。***

我们选择做这些事情…

> 不是因为它们容易，而是因为它们难。因为这个目标将有助于组织和衡量我们最好的能量和技能，因为这个挑战是我们愿意接受的，是我们不愿意推迟的。因此，当我们起航时，我们祈求上帝保佑这场人类有史以来最危险、最伟大的冒险。

听起来熟悉吗？[应该是](https://en.wikipedia.org/wiki/We_choose_to_go_to_the_Moon)。前几代人走在前面。在[#区块链](https://www.linkedin.com/search/results/content/?keywords=%23Blockchain&origin=HASH_TAG_FROM_FEED)领域，我们有责任继续推动创新。因为那些疯狂到认为自己可以改变世界的人，才是真正改变世界的人。这是

原文:

作者在文章中指出的功能甚至不在 ERC20 中——batch transfer 只是他们添加到自己的合同中的一个功能。标准如下:[https://theethereum.wiki/w/index.php/ERC20_Token_Standard](https://theethereum.wiki/w/index.php/ERC20_Token_Standard)

下面是他们写的随机函数:[https://medium . com/coin monks/alert-new-batchoverflow-bug-in-multiple-ERC 20-smart-contracts-CVE-2018-10299-511067 db 6536](/coinmonks/alert-new-batchoverflow-bug-in-multiple-erc20-smart-contracts-cve-2018-10299-511067db6536)

以下是避免使用 SafeMath 编写类似内容的方法。我想知道他们为什么把这篇文章命名为“新……bug”。本质上，这与任何类型的算法都可能发生的溢出类型相同，因为乘法只是重复的加法。

问题是可以避免的:对 uint256 使用 SafeMath

```
using SafeMath for uint256;
```

然后，调用 add 或 mul，而不是直接用+或*运算符进行加法或乘法，必要的溢出检查已经完成。

```
uint256 result = someNumber.mul(someOtherNumber);
```

SafeMath.mul 的实现:

```
/**  * @dev Multiplies two numbers, throws on overflow.  */  function mul(uint256 a, uint256 b) 
  internal pure returns (uint256 c) {if (a == 0) {      
        return 0;    
    }    
    c = a * b;    
    assert(c / a == b);    
    return c;  
}
```

[https://github . com/open zeppelin/zeppelin-solidity/blob/master/contracts/math/safe math . sol](https://github.com/OpenZeppelin/zeppelin-solidity/blob/master/contracts/math/SafeMath.sol)

阅读:[为什么是区块链？我们选择去月球。](https://www.linkedin.com/pulse/why-blockchain-because-we-choose-go-moon-dan-emmons/)

*丹·埃蒙斯是* [*埃蒙斯皮雷有限责任公司*](http://www.emmonspired.com/) *的所有者，是一名认证比特币专家、认证以太坊开发者、全栈开发者和加密货币项目顾问。他还是一个名为*[*# ByteSizeBlockchain*](https://www.youtube.com/watch?v=SVBZ7mdgGcA)*的 Youtube 频道和 iTunes 播客的创作者。*