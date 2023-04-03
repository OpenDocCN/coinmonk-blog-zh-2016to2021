# 分散式应用中的认证(DApps)

> 原文：<https://medium.com/coinmonks/authentication-in-decentralised-applications-dapps-2c7fd611248a?source=collection_archive---------5----------------------->

等等，为什么我们不能使用我们现有的认证系统，如谷歌 OAuth，脸书，电子邮件等。如果我们想的话，我们可以，但是这是不是有点矛盾，区块链的唯一目的不是保存任何类型的个人用户信息，它只是 p2p 网络，没有中间人。记住这一点，让我们看看如何认证我们的分散式应用。

![](img/2040facb458e1b47c8c452fc33b4c484.png)

Photo by [Micah Williams](https://unsplash.com/@mr_williams_photography?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

我认为 DApps 中的身份验证完全基于应用程序的类型，即我们的应用程序的用途，例如，如果是为了金融，我们不需要纯粹的身份验证系统，我们可以让用户使用加密钱包连接到我们的应用程序，就这样。我们的目的达到了，因为他们所要做的就是进行交易，而这不需要收集任何信息。

但是此外，如果我们想要一个合适的旧类型认证，我们可以通过使用智能契约轻松地做到这一点。

在这里，我使用以太坊区块链和智能合同写在坚实。

```
*// SPDX-License-Identifier: MIT* pragma solidity ^0.8;**contract** Registration{address **private** owner;**constructor**() {
owner = msg.sender;
}//Define your own type of user
//Do not save confidential information, even name is optional**struct** user{
bool isAuth;
string name;
}//Data structure to store our users
**mapping**(address => user) **public** User_Record;//modifier for validation
**modifier** checkAlreadyRegistered(address sender){
require(sender != owner,"owner cannot register themself.");
require(User_Record[sender].isAuth == false,"Already registered");
_;
}**function** getOwner()**view** **public** returns(address){
return owner;
}//function to register user
**function** register(string **memory** name)**public** checkAlreadyRegistered(msg.sender) {User_Record[sender].name = name;
User_Record[sender].isAuth = true;
}//function to signin user
**function** signin(address payable sender) **public view** returns(bool) {require(sender == msg.sender,"Cannot login someone else");
require(User_Record[msg.sender].isAuth == true,"Address not registered");return true;
}}
```

看，这很简单，你不必担心这么多的细节，只要以最佳方式设计你的用户就行了，因为记住区块链中的一切都是有成本的，所以你必须从各个方面进行优化。在这里，我们可以进一步优化我们的登录功能，以降低汽油成本。

```
 //function to signin user
function signin() public view returns(bool) {
require(User_Record[msg.sender].isAuth == true,”Address not registered”);
return true;
}
```

我们真的不需要用户手动键入公共地址，我们可以只使用我们的 msg.sender 和简单的认证。确保以这种方式实现登录功能，这样用户就不必为它进行交易，因为没有人想在每次登录时都付费，也就是说，使它成为不改变任何值的查看功能。上面的智能合同并不是最优化的，只是为了让我们能感受到它的要旨。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 另外，阅读

*   [AscendEx Staking](https://blog.coincodecap.com/ascendex-staking)|[Bot Ocean Review](https://blog.coincodecap.com/bot-ocean-review)|[最佳比特币钱包](https://blog.coincodecap.com/bitcoin-wallets-india)
*   [霍比审核](https://blog.coincodecap.com/huobi-review) | [OKEx 保证金交易](https://blog.coincodecap.com/okex-margin-trading) | [期货交易](https://blog.coincodecap.com/futures-trading)
*   [麻雀交换评论](https://blog.coincodecap.com/sparrow-exchange-review) | [纳什交换评论](https://blog.coincodecap.com/nash-exchange-review)
*   [美国最佳加密交易机器人](https://blog.coincodecap.com/crypto-trading-bots-in-the-us) | [经常性评论](https://blog.coincodecap.com/changelly-review)
*   [在印度利用加密套利赚取被动收入](https://blog.coincodecap.com/crypto-arbitrage-in-india)
*   [Godex.io 审核](/coinmonks/godex-io-review-7366086519fb) | [邀请审核](/coinmonks/invity-review-70f3030c0502) | [BitForex 审核](https://blog.coincodecap.com/bitforex-review)
*   [最佳比特币保证金交易](/coinmonks/bitcoin-margin-trading-exchange-bcbfcbf7b8e3) | [萝莉点评](/coinmonks/lolli-review-e6ddc7895ad8) | [比特币保证金交易](https://blog.coincodecap.com/bityard-margin-trading)
*   [创造并出售你的第一个 NFT](https://blog.coincodecap.com/create-nft) | [密码交易机器人](https://blog.coincodecap.com/best-crypto-trading-bots)
*   [如何在 CoinDCX 上购买柴犬(SHIB)币？](https://blog.coincodecap.com/buy-shiba-coindcx)
*   [折叠 App 回顾](https://blog.coincodecap.com/fold-app-review) | [LocalBitcoins 回顾](/coinmonks/localbitcoins-review-6cc001c6ed56) | [Bybit vs 币安](https://blog.coincodecap.com/bybit-binance-moonxbt)
*   [加密保证金交易交易所](/coinmonks/crypto-margin-trading-exchanges-428b1f7ad108) | [赚取比特币](/coinmonks/earn-bitcoin-6e8bd3c592d9) | [Mudrex 投资](https://blog.coincodecap.com/mudrex-invest-review-the-best-way-to-invest-in-crypto)
*   [WazirX vs coin dcx vs bit bns](/coinmonks/wazirx-vs-coindcx-vs-bitbns-149f4f19a2f1)|[block fi vs coin loan vs Nexo](/coinmonks/blockfi-vs-coinloan-vs-nexo-cb624635230d)
*   [比斯勒评论](https://blog.coincodecap.com/bitsler-review)|[WazirX vs coin switch vs coin dcx](https://blog.coincodecap.com/wazirx-vs-coinswitch-vs-coindcx)
*   [7 大顶级副本交易平台](https://blog.coincodecap.com/copy-trading-platforms) | [BuyCoins 审核](https://blog.coincodecap.com/buycoins-review)