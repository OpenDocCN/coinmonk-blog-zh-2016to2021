# 表征非金融资产——提高流动性

> 原文：<https://medium.com/coinmonks/tokenising-nfts-improving-liquidity-de9df6131ee9?source=collection_archive---------4----------------------->

上周，我写了一篇文章，介绍了一个叫做 [NFT 魂器](https://github.com/pringao-chevere/NFT-Horcrux)的基本概念验证，它允许将 NFT 标记为 ERC20 令牌。

为了避免你回头阅读那篇文章，这一段应该告诉你你需要知道的一切:

> 我不会说谎，这将是一篇技术文章。它围绕着一个我今天晚上下班后突然想到的概念验证，那个 [**NFT 魂器**](https://github.com/pringao-chevere/NFT-Horcrux) 。这个概念很简单，你发送一个 NFT 给代币商，然后它创建一个新的 ERC-20 代币合约来代表所有权的份额。总余额将转给您，您可以根据需要进行分配。

最初设计的主要缺点是，如果少数股权持有者拒绝合作和出售股权，NFT 可能会被锁定。此后的几天里，这个问题一直萦绕在我的脑海里，我认为我已经找到了一个相当不错的解决方案，不仅解决了这个问题，还为一些非金融资产中锁定的价值提供了更多的流动性。如果我们要在 DeFi 和 NFTs 之间架起一座桥梁，这是必须要做的事情。

![](img/9f58f2a69b13aaab5e83da2e03ea6948.png)

Photo by [Sarah Shaffer](https://unsplash.com/@sarah_elizabeth?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# NFT 魂器(v0.2)

该项目的主要流动性增强变化是 NFT 的 ETH 值在被令牌化时被声明。利用 ERC721 的`approve`功能，令牌化过程也得到了简化。

任何 NFT 都可以通过合同来象征化。令牌化过程将 NFT 转移到令牌化器，此时会创建一个新的可替换令牌，整个余额最初归创建者所有。

一旦象征化，NFT 仍然是象征化合约的财产，直到有人清算它，或拥有与总供应量相等的象征性余额，收回它。

当 NFT 被清算时，清算人提供的 ETH 等于 NFT 的价值(减去他们作为代币拥有的份额)。NFT 被转移给清算人，剩余的令牌持有者能够提取他们在存放的 ETH 中的份额。

当 NFT 被撤回时，NFT 被转移到撤回者手中，象征性契约被销毁。

仍有未申请 ETH 的清算 NFT 可以与之前约定的 NFT 价值和令牌供应合并(重新令牌化)。任何无人认领的 ETH 被转移到合并者，他们收到所有以前兑现的代币。

# 符号化

## 第一步。赞同

由于 ERC-721 的`onERC721Received`功能中的气体限制会阻止写入，第一步是批准 Tokeniser 合同，作为您要标记的 NFT 的运营商:

```
**function** approve(**address** _approved, **uint256** _tokenId)
```

这给予了令牌化合同转让 NFT 的许可，令牌化合同将在令牌化过程中进行转让。

## 第二步。象征化

然后调用标记器契约上的`tokenise`函数:

```
**function** tokenise(**address** _nftAddress,**uint** _tokenId, **uint** tokenCount, **uint** value) **public** **returns**(**address** newTokenAddress)
```

`tokenCount`是将铸造的 ERC-20 代币的总供应量。

`value`是被令牌化的 NFT 的 ETH 值。

`tokenCount * value < max value of uint256`有一个约束，是为了防止溢出线。

此函数返回新的可替换令牌合同的地址，供第三方合同使用。它还发出一个事件，下面将进一步讨论。

# 退出

撤销 NFTs 很简单，只需一步。如果一个地址拥有*所有*相应的令牌，那么他们只需调用 Tokeniser 契约上的`withdraw`。

```
**function** withdraw(**address** _nftAddress,**uint** _tokenId) **payable**;
```

这将自毁可替换令牌契约，并将 NFT 转移到`msg.sender`。

如果取款人拥有的代币少于所有相应的代币，他们必须以 ETH 支付差额，剩余的代币持有者可以使用相同的功能来兑换。

该函数还发出一个事件，下面将进一步讨论。

# 资产流动性

此次更新的主要目的是提高流动性，因为最初 NFT 将被锁定，直到最后的令牌持有者将其令牌交给任何想要撤回 NFT 的人。这就是为什么在令牌化期间设置`value`的原因，令牌持有者通过接受令牌暗示同意此估价。

因此，适当估值的象征性 NFT 可以根据需要被分割出售。在任何时候，有人同意价格是公平的，可以清算 NFT(支付他们不拥有的股份，并撤回 NFT)。如果 NFT 被低估，就会有经济动机促使人们以较低的价格将其变现，然后以较高的价格卖出。如果 NTF 被高估，估值师有责任以更低的价格重新标记(因为他们最初持有所有的标记)。

根据定义，清算后的 NFT 将意味着其他人仍持有代币。这些代币的价值是固定的，直到全部被赎回。NFT 可以被重新标记，而仍有拒绝赎回他们的 ETH 的人。这种反向清算过程称为“合并”，使用相同的`tokenise`功能。合并 NFT 会将之前兑换的代币以及之前为其他代币持有者保留的未兑换的代币转移给合并者。Solidation 含蓄地同意之前的 NFT 估值。

如果 NFT 已经清算，并且所有代币持有人都赎回了他们的 ETH，则可以用不同的估值和代币供应对 NFT 进行重新代币化。这是使用相同的`tokenise`功能完成的。

清算、NFT 提款和赎回都使用相同的`withdraw`功能。

# 事件

该合同有五个事件:

```
**event** Create(**address** **indexed** _nftAddress, **uint** **indexed** _tokenId, **address** location,**uint** value);
```

当 NFT 被标记化或重新标记化(但未合并)时，会发出此事件。它宣布了 NFT 的申报价值和新的代币合约位置。

```
**event** Destroy(**address** **indexed** _nftAddress, **uint indexed** _tokenId, **address** location);
```

当 NFT 被撤销并且令牌合同被销毁时，或者在彻底重新令牌化之前，会发出此事件。

```
**event** Liquidate(**address indexed** _nftAddress, **uint indexed** _tokenId, **uint** tokens);
```

此事件在 NFT 清算时发出，并宣布在此过程中消耗了多少相应的令牌。

```
**event** Solidate(**address indexed** _nftAddress, **uint indexed** _tokenId, **uint** tokens);
```

此事件在 NFT 合并时发出，并宣布向合并者重新颁发了多少相应的令牌。

```
**event** Withdraw(**address indexed** _nftAddress, **uint indexed** _tokenId, **uint** tokens);
```

当令牌持有者兑现其 ETH 时会发出此事件，并宣布在此过程中烧掉了多少相应的令牌。

# 其他考虑

虽然这个迭代确实大大提高了这个概念在现实世界中的可行性，但 NFT 魂器仍然没有为生产做好准备。一个关键的概念问题是，每一个符号化的 NFT 本质上只是另一个无名的狗屎硬币。即使 NFT 得到了正确的估价，相应的代币也很难被第三方接受。

将所有令牌化的 NFT 捆绑到单个令牌中，为高估 NFT 的人滥用打开了大门，以退出欺骗他人，由于 NFT 交易的集中性质，即使使用第三方市场数据或甲骨文也太容易被滥用(例如，我可以以 100 万美元的价格向自己出售垃圾令牌，以欺骗自动定价甲骨文)。

NFT 估值的主观性质可能意味着这种解决方案存在固有的限制，但这是未来版本的问题。

现在我已经把这个版本的 NFT 魂器部署到`[**0xc485eB25b7F380A22e11698413B20CA2A2546f94**](https://kovan.etherscan.io/address/0xc485eB25b7F380A22e11698413B20CA2A2546f94)`的科万测试网上

对你疯狂的钻石进行编码。