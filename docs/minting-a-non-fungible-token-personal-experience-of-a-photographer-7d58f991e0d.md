# 铸造一个不可替代的标志:一个摄影师的个人经历

> 原文：<https://medium.com/coinmonks/minting-a-non-fungible-token-personal-experience-of-a-photographer-7d58f991e0d?source=collection_archive---------0----------------------->

![](img/89e91d5fd9f88a55c30369dd81c70d61.png)

Photo by [Mark Fletcher-Brown](https://unsplash.com/@boab?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

这篇文章的目的是给出一个真实的例子，试图将 art 部署到不可替换的令牌(NFT)中。我不会试图在这里解释任何概念或术语，而只关注具体的行动及其结果。我相信这可能对今天的一小部分人和未来的更多人有所帮助，因为他们正在努力解决类似的问题。让我们开始吧..

**我的痛点是..**

我有一套数码照片，我通过传统的销售渠道进行销售。这些渠道具有严重的缺点:(I)由于大量供应的涌入，它们非常拥挤，(ii)它们没有认证机制，因此主要依赖于艺术家来限制可用版本的数量，(iii)它们缺乏复杂的金融交易机制，并且限于主要的法定货币，(iv)不可能有实际的部分所有权，以及(v)由于所有的销售渠道都是专有的，它们的解决方案和产品本质上是受监管的，并且自然不能保证在长时间内以相同的级别访问。

**我的替代方案是..**

我在读关于 NFTs 的文章，想着是否可以把我的照片铸造成代币，让它们永远留在区块链上。然而，我不知道从哪里开始。所以我开始四处寻找..

**市场？**

一年多前，我测试了 OpenBazaar，由于缺乏高质量的内容和对数字艺术的认真购买者，我感到非常失望。这在一般意义上是真实的，对于我感兴趣的更个性化的非功利主义照片来说更明显。所以我在寻找一个更新、更好、能容纳非功能性食物的地方。

快速搜索显示 OpenSea 是 NFT 的首要市场。大量的列表、各种各样的产品和价格，最重要的是成交记录都在那里。

因此，几天前和今天，我给这个网站打了高分。

然而，我如何在那里列出一些东西呢？

**铸造你的令牌**

为了在 OpenSea 上列出所需的合格 NFT。因此，我寻找一个可以创造它们的地方。最明显的来源是 [Rarible](https://app.rarible.com/) ，它本身就是公海上最大的商店。

因此，我去了 Rarible，很简单地开始创建一个数字收藏品。这个过程管理起来非常简单，但是创建一个列表最终花费了大约 7 美元，我认为这是一个不错的价格。

我可以在 Rarible 的 marketplace 上列出，也可以选择在 OpenSea 上列出。

**钱包是身份和标识符**

在这里，我应该后退一步，提到迄今为止的大部分账户管理都是由以太坊钱包处理的。这些站点中的大多数实际上都接受几个主要的站点，因此钱包成为存储 NFTs 的底层地址的标识符。因此，通过用钱包登录网站，网站读取地址，看到区块链并建立连接。

它并不总是无缝地工作，因为似乎有些网站并不更新区块链与你在他们的网站上做的关于 NFT 的所有行动，但一般来说它是有效的。

**回到 OpenSea，陷入一些困惑**

翻遍其他房源，我意识到有些房源有我的房源所缺乏的属性。比如这个精致的 [CyberGeisha](https://opensea.io/assets/0xd0c402bcbcb5e70157635c41b2810b42fe592bb0/10) ，它的属性中提到了文件类型。这是我意识到我的合同没有锁定资产的时刻。Rarible 允许链接或密钥，但实际的 JPG 不能作为可解锁的内容添加。

因此我被弄糊涂了。我创造了基于承诺交付基础资产的代币，但其他人创造了锁定这些资产的代币。所以我需要找到一种方法来铸造令牌并锁定资产。

但在此之前，我想知道我创造的令牌在哪里？更不用说在此期间，我一直在支付费用来列出东西，并允许网站使用钱包。鉴于高昂的油价，这最终也要花费一大笔钱。

无论如何，我决定令牌的住所应该在可解锁的内容之前解决，我去了以太网扫描并查看了我的交易。那一刻，我意识到我的代币自然存在于合同之下，例如 Rarible 或其他人创建的合同。因此，我的地址中有令牌引用，但实际的智能合同和令牌不在我的保管之下(根据我有限的技术理解)。

所以现在我有一个所有权问题和一个可解锁的内容技术问题。我继续阅读和查看其他网站..

**添加可解锁内容的简单方法？**

我的下一站是 [Cargo](https://www.cargo.build/) ，它就像一个稀有的市场和 DIY 铸造服务。货物有一个不同的定价结构，最终与 ETH 联系在一起。它的主要优势是能够无缝添加可解锁的数字内容。与 Rarible 类似，Cargo 也无缝连接到 OpenSea，以覆盖更广泛的受众。因此，我重复了与之前相同的步骤，并创建了另一个令牌。因此，我确保有一种方法可以向令牌添加内容，在我的情况下是照片。

但我还是想看看这个设置会随着一份与我的账户相关联的合同发生什么变化，我可以在这个账户上为每张照片铸造代币。

**寻求合同交易**

在进一步阅读后，我总结了我的选择，创建一个艺术家合同或基本上 ERC 721 兼容的合同，我会设置名称和符号等。我决定从两个选项中选择:[infinfint](https://www.infinft.com/#mint)和 [Mintable](https://mintable.app/) 。这两个网站用不同的方法提供相同的服务，我决定选择 infiNFT，因为他们在 OpenSea 上有高质量的列表，我认为这符合我的想法。

当我试图创建联系人时，infiNFT 收取了 0.1 ETH 的费用，我认为这是合理的。

然而，考虑到天然气成本，该合同的最终成本超过 0.4 ETH。

虽然我认为这是非常陡峭的，我检查了 Mintable 的成本后，才看到它是 40%左右低。我会假设这是由于不同的气体使用超过任何事情，但我没有仔细检查这一点。

现在是时候在区块链永久保存一些照片了。

你想..

高速公路的终点在哪里？

到这一点，8 个小时过去了，在我自己的合同上铸造一张照片将是这个场合的一个很好的结局。挣扎了一会儿才在以太扫描上找到联系人 ID，然后我小心翼翼地选择了一张照片，填写了所有细节，然后点击了 Mint。

结果出来了:0.005 ETH 费——好。大约 0.581 ETH 的汽油成本—什么？

尽管我知道汽油费很高，但一张照片 200 多美元的价格超出了我的预期。我决定再等一会儿，随着汽油价格的上涨，价格持续上涨，接近 400 美元。

当时，我试图调整交易用气量，但由于合同相关的技术问题，这证明是不可行的。手头有了这些信息，我决定联系 INF inft Discord 团队，寻求他们的建议。

在我们最初的对话中，我尝试了不同的文件大小，并调整了描述的长度，这影响了令牌的字节大小，但不影响它的 gas 使用。我期待着他们的进一步指导。

*更新 1:指导居然来了，很有帮助。我和 infiNFT 开发者 Natealex 聊天，他指出我使用了错误的合同 ID。这是由于 ETH 事务花费了太多时间而超时造成的。*

*一旦 Nate 解决了 ID 问题，即使油价上涨，铸造成本也降至 18 美元左右。*

**状态和我学到了什么..**

大约 12 小时后，成本仍然很高，为我铸造一堆代币是不可行的。

我从这次经历中了解到，燃气费用是一个主要的障碍，这使得以太坊网络的使用受到了限制。虽然这看起来像是简单的供需动态，但实际问题是以太坊上运行着非常不同的垂直行业。因此，一方面的高成本可能比另一方面更有害。在这个例子中，明显的影响是由于成本限制了访问，而成本是 mainnet 上其他地方的需求因素。

此外，我还发现了一些正在帮助发展 NFT 生态系统的公司/项目。这是一个我期待有巨大增长和创造力的领域。这些公司有很多需要改进的地方，尤其是在支持和文档方面，但他们正在朝着正确的方向前进。

最后，有了这次经历，我意识到如果有一篇这样的文章，我会花少得多的时间和精力来达到同样的目的。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   开发人员的最佳加密 API
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   杠杆代币的终极指南
*   [加密交易的最佳 VPN](https://coincodecap.com/best-vpns-for-crypto-trading)
*   [最佳加密分析或链上数据](https://coincodecap.com/blockchain-analytics) | [Bexplus 评论](https://coincodecap.com/bexplus-review)
*   [NFT 十大市场造币集锦](https://coincodecap.com/nft-marketplaces)
*   [AscendEx Staking](https://coincodecap.com/ascendex-staking)|[Bot Ocean Review](https://coincodecap.com/bot-ocean-review)|[最佳比特币钱包](https://coincodecap.com/bitcoin-wallets-india)
*   [Bitget 回顾](https://coincodecap.com/bitget-review)|[Gemini vs block fi](https://coincodecap.com/gemini-vs-blockfi)|[OKEx 期货交易](https://coincodecap.com/okex-futures-trading)