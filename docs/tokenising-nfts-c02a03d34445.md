# 标记 NFT

> 原文：<https://medium.com/coinmonks/tokenising-nfts-c02a03d34445?source=collection_archive---------5----------------------->

这个标题听起来可能有点重复，因为 NFT 的 T 已经代表了 *token* ，但是我可以向你保证你将要读到的是有意义的。

既然 NFT 已经开始以相当疯狂的价格出售，他们真的已经成为他们自己的资产。随着不可替代市场的蓬勃发展，DeFi 也在平行增长，似乎有一种方法来标记 NFT 的所有权可能会成为一个非常方便的技巧。

![](img/ea17ebfe492e5678bf32ee34ba53c95c.png)

Photo by [Ali Yahya](https://unsplash.com/@ayahya09?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# NFT 魂器

我不会说谎，这将是一篇技术文章。它围绕着我今天晚上下班后突然想到的一个概念验证，即 [**NFT 魂器**。这个概念很简单，你发送一个 NFT 给代币商，然后它创建一个新的 ERC-20 代币合约来代表所有权的份额。总余额将转给您，您可以根据需要进行分配。](https://github.com/pringao-chevere/NFT-Horcrux)

要退出 NFT，你需要拥有 100%的股份。撤回将把 NFT 转让给你和 ERC-20 的合同。

*注意:我对这个项目做了重大修改，并在后续文章中写道:* [***标记 NFTs——提高流动性***](/coinmonks/tokenising-nfts-improving-liquidity-de9df6131ee9) *。从概念上讲，这篇新文章涵盖了您将在这里读到的所有内容，但是所描述的契约还增加了一些特性。*

# 符号化

由于 ERC-721 的`onERC721Received`功能中的气体限制会阻止写入，令牌化过程需要 3 个步骤:

## 第一步。最好的

第一步是调用 Tokeniser 契约上的`prime`函数:

```
**function** prime(**address** _nftAddress,**uint** _tokenId)
```

这告诉合同你将要从你当前的地址存入一个特定的 NFT。`_nftAddress`是 NFT 合同的地址。

## 第二步。安全转移自

接下来，使用`safeTransferFrom`将 NFT 转移到令牌化器契约:

```
**function** safeTransferFrom(**address** _from, **address** _to, **uint256** _tokenId, **bytes memory** data) **public**
```

如果您没有正确准备，该交易将会失败，但是使用`transferFrom`不可能进行这样的检查，所以确保您使用前者是安全的。否则你可能会失去你的 NFT(也就是说，如果你没有正确启动它，它就会卡在合同里)。

如果你确实使用了`transferFrom`,但是你已经启动了合同，那么它仍然会很好地工作。

## 第三步。象征化

最后调用标记器契约上的`tokenise`函数:

```
**function** tokenise(**address** _nftAddress,**uint** _tokenId, **uint** tokenCount) **public returns**(**address** newTokenAddress)
```

您可以使用`tokenCount`指定想要将您的 NFT 分成多少个可替换的代币。这个函数返回新的可替换令牌契约的**地址**，以防您需要它。由于前端原因，还会发出一个`Create`事件。

```
**event** Create(**address indexed** _nftAddress, **uint indexed** _tokenId, **address** location);
```

# 退出

撤销 NFTs 很简单，只需一步。如果一个地址拥有*所有*相应的令牌，那么他们只需在令牌化器契约上调用`withdraw`。如果他们不这样做，它就会失败。

```
**function** withdraw(**address** _nftAddress,**uint** _tokenId) **public**
```

这将自毁可替换令牌契约，并将 NFT 转移到`msg.sender`。它还放出一个`Destroy`事件:

```
**event** Destroy(**address indexed** _nftAddress, **uint indexed** _tokenId, **address** location);
```

# 尝试一下

这只是一个概念的证明，一个我下班回家时想在晚上实现的想法。在`0x72253babDF6ABdF09F90caA3640898A111F74795`的 **Kovan** testnet 上部署了一个 Tokeniser 供人们使用，但实际上我不认为有人会想使用这个确切的实现。

例如，有一些围绕少数利益相关者的问题需要一些奇特的 DAO 机制来解决。我只是想说明标记 NFT 是可能的(而且很容易！).

玩得开心。