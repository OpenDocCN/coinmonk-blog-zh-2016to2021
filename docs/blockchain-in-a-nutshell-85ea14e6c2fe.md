# 简而言之，区块链

> 原文：<https://medium.com/coinmonks/blockchain-in-a-nutshell-85ea14e6c2fe?source=collection_archive---------4----------------------->

## 区块链基础知识再探

## 了解技术背后的高级流程

区块链很容易理解——只是有很多技术术语和行话主导着这个行业的话语。让我们鸟瞰一下它是如何工作的。

![](img/06e5a0d351a43780afe9a5c5da98e5aa.png)

Photo by [CHUTTERSNAP](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 区块链术语

*开始把区块链想象成一台电脑——现在你只需要调整一些术语，并意识到这不是一台电脑，而是一千台电脑在全世界运行。*

## 数据=状态

就像计算机存储 word 文档、excel 表格和 powerpoint 演示文稿一样，区块链存储数据。用区块链的术语来说，组合起来的数据量被称为区块链的*状态*。该数据作为通用状态层可供所有网络参与者使用。这就是区块链与互联网的不同之处:它有一个全球状态，每个人都可以访问——就像你可以访问硬盘上的每个文件一样。

## 计算机=节点

区块链不是一台单独的计算机；它由数百或数千台不同的计算机组成，所有这些计算机都提供它们的处理能力，以形成一个全球性的、分散的虚拟机。这些单独的计算机被称为*节点*或*验证器*，它们的努力得到了回报，主要是网络的本地令牌。这就是臭名昭著的*挖掘*比特币的过程:因为他们保持网络运行，节点获得奖励。

## 程序=智能合同

就像你可以打开、更改和删除你电脑上的文件一样，区块链可以改变它的数据。在你的计算机上，这主要是通过程序来完成的，例如文本编辑器或图像处理程序。在区块链，这些程序通常被称为智能合约。一些区块链可以不需要它们，但是具有智能合同功能的区块链通常可以满足更复杂的用例。

## 交易=用户操作

为了执行程序的功能(也称为。智能合约的一种方法)，你必须向网络提交交易。区块链网络就像互联网上的应用一样，容易受到垃圾邮件的攻击。这通常是以攻击者向网络提交无休止的事务并堵塞其容量的形式发生的。为了防止这种情况发生，每笔交易通常都与交易成本相关联，通常被称为*气*。这是一种防止网络垃圾邮件的安全机制。

## 私钥=身份

对于大多数交易来说，知道发送信息的人被授权这样做是很重要的。这是由椭圆密码保证的。每个用户都有一个由私钥和公钥组成的密钥对。每笔交易都用用户的私钥签名，只有用户有权使用。签名后，任何人只需查看用户的公钥就可以验证这个签名，而不需要知道私钥。

## 钱包=身份管理者和接入点

大多数用户交易是通过钱包发送的，钱包是存储用户私钥的程序，可以作为区块链的接入点，以便直接从钱包提交交易。使用钱包的好处是用户不需要手动插入和管理私钥。钱包有各种形状和大小，从软件到硬件到纸质钱包。

# 真实世界的例子

既然我们已经学习了基本术语，让我们来看一个真实世界的例子:一种基于区块链的货币，又名。加密货币。

## 加密货币=资产负债表

大多数加密货币都是基于智能合约的，通常这些程序只存储简单的资产负债表:一列列出用户的账户地址，另一列列出相应的代币数量。该程序允许通过公开一些方法来实现基本功能，例如转移令牌或获取用户的余额。

![](img/39bf5fed61eabcad640c0b0e42fbd39f.png)

Photo by [André François McKenzie](https://unsplash.com/@silverhousehd?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

如果用户想要将一些代币转移到另一个账户，他们指定他们想要将多少代币发送到什么地址，用他们的私钥签署交易，并通过他们的钱包将其发送到网络。智能合约然后检查用户的签名；如果它是正确的，并且用户有足够的余额，用户事务可以运行，将令牌转移到另一个帐户。

转账本身只需要更新全局状态:发送方的余额减少，接收方的余额增加指定的金额，减去网络接受交易所需的任何交易费用。

## 分散化的安全性

这个例子凸显了分权的力量。上面概述的加密货币的安全转移是唯一可能的，因为数据不在中央计算机上，其中一方可以任意改变它以改变用户的余额；它受到加密方法和分散的数千台计算机网络的保护，这些计算机分布在全球各地，不断检查彼此的工作。

计算机检查彼此工作的方法被称为*一致算法*。这种机制防止恶意行为者向网络提交被操纵的数据，例如，我们上面的加密货币的突变资产负债表。虽然有许多不同种类的共识算法可用(*工作证明*、*利益证明*、*委托利益证明*、*授权证明*等等)，但它们都有相同的潜在目标:让恶意行为变得高度不可行，从而变得极不可能。

# 区块链实现了什么

*区块链的主要特点是，它使您能够通过加密货币、非功能性交易等方式将现实世界的价值带入数字领域。*

## 货币=可替代的代币

到目前为止，它们不是区块链技术的唯一用例，但可能是最突出的一个:加密货币。第一个区块链网络——比特币——确实提供了这个用例。通过加密货币，金融价值可以进入数字领域，通过智能合同，可以对其进行编程，以实现更加透明和高效的支付流程。

![](img/67f5f731ae18be6b1e5b9c3661c07a1d.png)

Photo by [Alexander Schimmeck](https://unsplash.com/@alschim?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 艺术品和收藏品=不可替代的代币(NFT)

除了钱之外，任何个人艺术作品、收藏品、游戏项目等等都可以通过不可替代的代币(NFT)在数字领域中被创造出来。不可替代性仅仅意味着每一件物品都是独一无二的，不能与其他物品互换使用，例如艺术品。NFT 市场已经是一个价值数十亿美元的市场，并且每天都在增长。

## 一些你还没见过的东西

除了可替换和不可替换的令牌之外，区块链技术还可以用于透明、安全和可靠的结果至关重要的任何地方，例如身份识别、投票和治理、供应链透明度等等。这项技术仍处于起步阶段，可能还有数以千计的新用例有待探索。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 另外，阅读

*   [什么是保证金交易](https://blog.coincodecap.com/margin-trading) | [美元成本平均法](https://blog.coincodecap.com/dca)
*   [BigONE 交易所评论](/coinmonks/bigone-exchange-review-64705d85a1d4) | [电网交易 Bot](https://blog.coincodecap.com/grid-trading)
*   [新加坡十大最佳加密交易所](https://blog.coincodecap.com/crypto-exchange-in-singapore) | [收购 AXS](https://blog.coincodecap.com/buy-axs-token)
*   [投资印度的最佳加密软件](https://blog.coincodecap.com/best-crypto-to-invest-in-india-in-2021) | [WazirX P2P](https://blog.coincodecap.com/wazirx-p2p)
*   [7 个最佳零费用加密交换平台](https://blog.coincodecap.com/zero-fee-crypto-exchanges)
*   [分散交易所](https://blog.coincodecap.com/what-are-decentralized-exchanges) | [比特 FIP](https://blog.coincodecap.com/bitbns-fip)
*   [用信用卡购买密码的 10 个最佳地点](https://blog.coincodecap.com/buy-crypto-with-credit-card)
*   [加拿大最佳加密交易机器人](https://blog.coincodecap.com/5-best-crypto-trading-bots-in-canada) | [Bybit vs 币安](https://blog.coincodecap.com/bybit-binance-moonxbt)
*   [火币的加密交易信号](https://blog.coincodecap.com/huobi-crypto-trading-signals) | [Swapzone 审查](/coinmonks/swapzone-review-crypto-exchange-data-aggregator-e0ad78e55ed7)
*   最佳[密码交易机器人](https://blog.coincodecap.com/best-crypto-trading-bots) | [购买索拉纳](https://blog.coincodecap.com/buy-solana) | [矩阵导出评论](https://blog.coincodecap.com/matrixport-review)
*   [Coldcard 评论](https://blog.coincodecap.com/coldcard-review) | [BOXtradEX 评论](https://blog.coincodecap.com/boxtradex-review)|[unis WAP 指南](https://blog.coincodecap.com/uniswap)
*   [阿联酋 5 大最佳加密交易所](https://blog.coincodecap.com/best-crypto-exchanges-in-uae) | [SimpleSwap 评论](https://blog.coincodecap.com/simpleswap-review)
*   购买 Dogecoin 的 7 种最佳方式 | [ZebPay 评论](https://blog.coincodecap.com/zebpay-review)
*   [最佳期货交易信号](https://blog.coincodecap.com/futures-trading-signals) | [流动性交易回顾](https://blog.coincodecap.com/liquid-exchange-review)
*   [3 商业评论](/coinmonks/3commas-review-an-excellent-crypto-trading-bot-2020-1313a58bec92) | [Pionex 评论](/coinmonks/pionex-review-exchange-with-crypto-trading-bot-1e459d0191ea) | [Coinrule 评论](/coinmonks/coinrule-review-2021-a-beginner-friendly-crypto-trading-bot-daf0504848ba)
*   [莱杰 vs n rave](/coinmonks/ledger-vs-ngrave-zero-7e40f0c1d694)|[莱杰 nano s vs x](/coinmonks/ledger-nano-s-vs-x-battery-hardware-price-storage-59a6663fe3b0) | [币安评论](/coinmonks/binance-review-ee10d3bf3b6e)
*   [Bybit 交易所评论](/coinmonks/bybit-exchange-review-dbd570019b71) | [Bityard 评论](https://blog.coincodecap.com/bityard-reivew) | [CoinSpot 评论](https://blog.coincodecap.com/coinspot-review)
*   [3 commas vs crypto hopper](/coinmonks/3commas-vs-pionex-vs-cryptohopper-best-crypto-bot-6a98d2baa203)|[赚取秘密利息](/coinmonks/earn-crypto-interest-b10b810fdda3)
*   最好的比特币[硬件钱包](/coinmonks/hardware-wallets-dfa1211730c6) | [BitBox02 回顾](/coinmonks/bitbox02-review-your-swiss-bitcoin-hardware-wallet-c36c88fff29)
*   [BlockFi vs 摄氏度](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630) | [Hodlnaut 审核](/coinmonks/hodlnaut-review-best-way-to-hodl-is-to-earn-interest-on-your-bitcoin-6658a8c19edf) | [KuCoin 审核](https://blog.coincodecap.com/kucoin-review)
*   [Bitsgap 审查](/coinmonks/bitsgap-review-a-crypto-trading-bot-that-makes-easy-money-a5d88a336df2) | [Quadency 审查](/coinmonks/quadency-review-a-crypto-trading-automation-platform-3068eaa374e1) | [Bitbns 审查](/coinmonks/bitbns-review-38256a07e161)