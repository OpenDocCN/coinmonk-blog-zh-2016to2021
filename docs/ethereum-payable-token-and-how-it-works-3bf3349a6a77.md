# 以太坊支付令牌及其工作原理

> 原文：<https://medium.com/coinmonks/ethereum-payable-token-and-how-it-works-3bf3349a6a77?source=collection_archive---------1----------------------->

## ERC1363 诞生的故事

你们每个人肯定都听说过 **ERC20 代币**。
在本文中，我将讨论如何将它们用于现实生活中的支付，以及为什么我要构建 **ERC1363 可支付令牌**。
在这里你可以找到 ERC1363 [EIP](https://eips.ethereum.org/EIPS/eip-1363) 和[实现](https://github.com/vittominacori/erc1363-payable-token)。

![](img/a5e53ea55cbccaa4b198c30aa6312bbf.png)

## **什么是 ERC20 令牌？**

正如 EIP 20 所说:

> ERC20 提供了转移令牌的基本功能，并允许令牌被批准，以便它们可以由链上的另一个第三方使用。

所以基本上你可以用代币来代表任何可替换的可交易商品:硬币、忠诚度积分、游戏积分等等。

这里的标准 ERC20 定义:

ERC20 interface definition

这里是`transfer`函数的一个基本实现。

ERC20 transfer implementation

下面是`approve`和`transferFrom` 功能的基本实现。

ERC20 Approve workflow implementation

## 代币余额

所以，可以看出，`balances`无非是代币持有者地址与其代币数量之间的一种映射。

例如，假设代币合约有两个代币持有者:

*   `0x111...111`余额为 100 个单位
*   `0x222...222`余额为 200 单位

令牌合同的`balances`数据结构将包含以下信息:

```
balances[0x111...111] = 100
balances[0x222...222] = 200
```

`balanceOf(...)`函数将返回以下值:

```
tokenContract.balanceOf(0x111...111); // will return 100
tokenContract.balanceOf(0x222...222); // will return 200
```

## 转移代币余额

如果`0x111...111`想要向`0x222...222`传送 10 个令牌，`0x111...111`将执行该功能:

```
tokenContract.transfer(0x222...222, 10);
```

令牌契约的`transfer(...)`函数将改变`balances`数据结构，以包含以下信息:

```
balances[0x111...111] = 90
balances[0x222...222] = 210
```

`balanceOf(...)`函数现在将返回以下值:

```
tokenContract.balanceOf(0x111...111); // will return 90
tokenContract.balanceOf(0x222...222); // will return 210
```

## 批准和转移代币余额

`approve(...)`和`transferFrom(...)`用于当某人想要授权另一个地址代表其消费代币时。

同样，`allowed`只不过是代币持有者地址、消费者地址和批准的代币数量之间的映射。

因此，如果`0x111...111`想要授权`0x222...222`转移一些代币(30)，`0x111...111`将执行该功能:

```
tokenContract.approve(0x222...222, 30);
```

`approve`数据结构现在将包含以下信息:

```
allowed[0x111...111][0x222...222] = 30
```

如果`0x222...222`想要稍后将一些代币(20)从`0x111...111`转移到`0x333...333`，`0x222...222`执行`transferFrom(...)`功能:

```
tokenContract.transferFrom(0x111...111, 0x333...333, 20);
```

`balances`数据结构将被修改，以包含以下信息:

```
balances[0x111...111] = 70
balances[0x222...222] = 210
balances[0x333...333] = 20
```

并且`allowed`数据结构现在将包含以下信息:

```
allowed[0x111...111][0x222...222] = 10
```

`0x222...222`还能从`0x111...111`消费 10 个代币。

`balanceOf(...)`函数现在将返回以下值:

```
tokenContract.balanceOf(0x111...111); // will return 70
tokenContract.balanceOf(0x222...222); // will return 210
tokenContract.balanceOf(0x333...333); // will return 20
```

## 有什么问题？

> 一切都很好，但是，如果令牌接收者希望在收到令牌或令牌批准后采取行动，该怎么办呢？

假设您正在发送令牌来支付 ACME#1 发票，那么接收方契约应该将您的发票设置为已支付。
这怎么可能？没有人通知其他任何人该合同已经收到支付发票号 ACME#1 的令牌。

或者，假设您希望在批准使用订阅管理器合同中的令牌后启用订阅。

或者想象一下…你在现实生活中可以通过付出一些东西并得到其他回报来做的其他事情。

所以，实际上，解决方案可能是:

*   对接收方契约进行`approve`操作，然后对契约方法进行另一个交易，以通知您刚刚批准它使用您的令牌(这由用户负责)

或者

*   您完成了您的事务，然后接收方契约的系统应该“监听”事件`Transfer`或`Approval`并完成另一个事务以结束循环(这由开发人员负责)

无论如何，这些解决方案预计在至少 2 次交易中完成，这意味着双倍的消费费用、双倍的等待时间、大量的复杂性等等。

从我作为智能合同和 DAPP 开发人员的角度来看，这意味着我必须设置和维护一个带有 nodejs 服务的 GETH 节点，监听来自令牌合同的任何传输事件，调用接收方合同上的任何回调以设置完成的内容，更新汽油成本以快速完成我的交易，并且不支付超过收到的令牌值的汽油，以及…哦哦哦…多少事情。

这就是我决定构建自己的可支付令牌实现的原因。

## 没有人已经有了解决方案？

是的，我谷歌了很多次，当然。许多人已经探索了这个问题，并且有许多其他的 ERC 和提议已经被许多令牌使用，但是还没有一个标准。

例如，您可以使用`approveAndCall`方法来批准并调用接收者契约上的回调，或者使用 [ERC677](https://github.com/ethereum/EIPs/issues/677) 来添加允许调用接收者契约上的函数的`transferAndCall`方法，或者使用 [ERC223](https://github.com/ethereum/EIPs/issues/223) 来调用标准的`transfer`方法。
每一个都需要在接收者契约上实现不同的回调函数。

**付费代币**的诞生是在我对 [ERC721](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-721.md) 或众所周知的不可替代或可收集代币进行更多研究的时候。

ERC721 已经被接受并合并为标准 EIP，因此这意味着它的方法可以扩展用于类似的目的。
它要求必须实现 [ERC165](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-165.md) 以探索契约支持哪些接口，并且如下所述，如果钱包/经纪人/拍卖应用程序将接受安全 ERC721 传输，则必须实现钱包接口。

ERC721 Token Receiver

## 什么是 ERC1363 付费代币？

它定义了在`ERC1363Receiver`契约上调用`onTransferReceived`的`transferAndCall`和`transferFromAndCall`函数。
它定义了在`ERC1363Spender`契约上调用`onApprovalReceived`的`approveAndCall`函数。

通过这样做，合同可以接受 ERC-20 支付，以创建一个可支付代币的众筹销售，出售代币服务，支付发票，进行订阅，或将它们用于特定的公用事业和许多其他目的。

听起来不错？

除了 ERC20 和 ERC165 接口之外，每个符合 Payable Token 标准的合同都必须实现`ERC1363`接口。

ERC1363 Interface

想要通过`transferAndCall`或`transferFromAndCall`接受象征性支付的契约必须实现`ERC1363Receiver`接口:

`ERC1363Receiver Interface`

想要通过`approveAndCall`接受象征性支付的契约必须实现`ERC1363Spender`接口:

`ERC1363Spender Interface`

这里有我自己使用 OpenZeppelin 库实现的一个 **ERC1363 Token** 。

[](https://github.com/vittominacori/erc1363-payable-token) [## vittominacori/er c 1363-应付-令牌

### ERC-1363 可支付令牌-vittominacori/ERC 1363-可支付令牌的代码实现

github.com](https://github.com/vittominacori/erc1363-payable-token) 

如有任何建议，敬请评论[官方 EIP](https://eips.ethereum.org/EIPS/eip-1363) 或向我的[代码实现](https://github.com/vittominacori/erc1363-payable-token)发帖。

您可以通过安装 npm 在项目中使用它:

```
npm install erc-payable-token
```

遵循 [GitHub](https://github.com/vittominacori) 、 [Twitter](https://twitter.com/vittominacori) 或 [LinkedIn](https://www.linkedin.com/in/vittoriominacori/) 上的 [Vittorio Minacori](https://medium.com/u/29e8f645011e?source=post_page-----3bf3349a6a77--------------------------------) 。