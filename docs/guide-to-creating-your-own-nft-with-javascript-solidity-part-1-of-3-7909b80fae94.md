# 使用 Javascript 和 Solidity 创建自己的 NFT 指南—更新后包括视频指南——🚀(第 1 部分，共 3 部分)

> 原文：<https://medium.com/coinmonks/guide-to-creating-your-own-nft-with-javascript-solidity-part-1-of-3-7909b80fae94?source=collection_archive---------0----------------------->

![](img/7e07d92798550f9f1ee8589fd73c34e3.png)

# 什么是 NFT？

NFT 代表不可替代的令牌。不可替代是一个经济术语，你可以用它来描述你的家具、一个歌曲文件或者你的电脑。这些东西不能与其他物品互换，因为它们有独特的属性。

NFT 世界相对较新。理论上，NFTs 的范围是任何需要可证明所有权的独特事物。

如需更深入的解释，请查看此[ [**链接**](https://ethereum.org/en/nft/)

# 什么是加密钱包？

区块链/加密钱包是一种数字钱包，允许用户存储和管理他们的比特币、以太和其他加密货币。

当创建一个允许用户与 NFTs 交互的网站时，您需要将您的钱包连接到该网站。

最常用的钱包是[ [**Meta Mask**](https://metamask.io/) ]，这个可以作为 Chrome 和 Firefox 的浏览器扩展。

查看 [**这里**](https://www.coinbase.com/learn/crypto-basics/what-is-a-crypto-wallet) 】更深入的解释区块链钱包。

# 你如何创造一个 NFT？

不需要任何开发经验，有许多方法可以创建 NFT，一个很好的例子就是[ [**Opensea**](https://opensea.io/) ]。

您可以创建一个 Opensea 帐户，将其链接到您的加密钱包(MetaMask ),并像创建任何其他 web 应用程序一样，通过与网站进行交互来创建 NFTs。

虽然这是一个很好的选择，但我们不会在我们的例子中这样做，我们将陷入成为区块链开发者的兔子洞🚀。

为了创建我们的 NFT，我们需要做一些事情:

*   用[ [**Solidity**](https://docs.soliditylang.org/en/v0.8.7/introduction-to-smart-contracts.html) ](这是区块链上使用的语言)编写智能合同
*   将您的合同部署到您选择的网络
*   创建您的 NFT 资产
*   为 NFT 创建相应的元数据文件
*   使用合同将元数据文件创建到区块链

# 写你自己的 NFT 合同

智能合约的伟大之处在于，你可以利用很多“锅炉板块”。这些最大的提供者是[ [**开齐柏林**](https://openzeppelin.com/) ]。

“OpenZeppelin 合约通过为以太坊和其他区块链使用经过战斗考验的智能合约库，帮助您最大限度地降低风险。它包括最常用的 ERC 标准的实现。”

作为开发人员，这对于我们来说非常好，因为我们可以轻松地创建合同。

一个 NFT 契约又名一个[**【ERC 721 契约】**](https://eips.ethereum.org/EIPS/eip-721) 允许我们创建/铸造和购买/转移数字资产/NFT 的所有权。

以下是真实世界 NFT 合同的完整可靠性示例:

```
*// SPDX-License-Identifier: MIT*pragma solidity >=0.6.0 <0.8.0;import "@openzeppelin/contracts/token/ERC721/ERC721.sol";import "@openzeppelin/contracts/access/Ownable.sol"; contract NFT *is* ERC721, Ownable { address payable public _owner; mapping(uint256 => bool) public sold; mapping(uint256 => uint256) public price; event Purchase(address owner, uint256 price, uint256 id, string uri); constructor() ERC721("YOUR TOKEN", "TOKEN") { _owner = msg.sender; }function mint(string memory _tokenURI, uint256 _price) public onlyOwner returns (bool) { uint256 _tokenId = totalSupply() + 1; price[_tokenId] = _price; _mint(address(this), _tokenId); _setTokenURI(_tokenId, _tokenURI); return true; }function buy(uint256 _id) external payable { _validate(_id);    _trade(_id); emit Purchase(msg.sender, price[_id], _id, tokenURI(_id)); } function _validate(uint256 _id) internal { require(_exists(_id), "Error, wrong Token id"); require(!sold[_id], "Error, Token is sold"); require(msg.value >= price[_id], "Error, Token costs more");  } function _trade(uint256 _id) internal { _transfer(address(this), msg.sender, _id); _owner.transfer(msg.value); sold[_id] = true; }}
```

上面的例子扩展了两个现有的 openzeppelin 合同，使您能够满足[**【ERC 721】**](https://eips.ethereum.org/EIPS/eip-721)所需的全部规格。

要注意的主要事情是传递到构造函数中的两个字符串第一个“您的令牌”是使用契约创建的所有令牌的父名称，第二个参数“令牌”是将用于您的令牌的短代码。

# 部署您的第一份 NFT 合同

一旦你写了你的合同，你需要部署它来测试它，关于区块链需要注意的一件重要的事情是所有的合同都是不可变的，这意味着一个 bug 将永远存在于你的合同中，一旦它被部署，你就不能对它进行修改😢。

幸运的是，我们有可用的测试环境，有多个测试网络可用。我最喜欢使用的测试网是 Rinkeby，这里有一些关于 [**【测试网】**](https://docs.ethhub.io/using-ethereum/test-networks/) **的进一步阅读。**

测试网的一个巨大好处是，你可以重新设置你的合同和覆盖错误。这意味着您可以测试您的合同，直到您满意为止，然后部署到主网(在那里它是不可变的)

要在测试网上获得你的合同，你需要使用[**【Truffle】**](https://www.trufflesuite.com/)，Truffle 是一个优秀的工具，可以让你轻松部署。

我不会去经历松露部署，但 [**【此视频】**](https://www.youtube.com/watch?v=CgXQC4dbGUE&t=78s&ab_channel=DappUniversity) 会给你所有你需要的信息。

# 免费视频[代码演练]

我已经录制了一个视频走过样板代码[【链接】](https://www.youtube.com/watch?v=F0xD1DK3pe4&ab_channel=G)。我真的希望这能帮助其他希望进入区块链开发的开发者。

# Crypto Ghoulz

为了研究这个话题，我在 polygon network 上创建了自己的区块链网站，允许用户购买 NFT，检查一下，如果你感觉辣，就购买我的 NFT。[https://cryptoghoulz.com](https://cryptoghoulz.com/)。

如果您对 [**【密码幽灵】**](https://cryptoghoulz.com) 有任何疑问，请通过 [**【推特】**](https://twitter.com/Crypto_Ghoulz) 联系。

# **项目代码**

如果你想在这方面开始，你可以找到一个锅炉板回购 [**【这里】**](https://github.com/gary-george/nft-react-boilerplate) 。

这是一个很好的项目，可以帮助你真正感受到一切是如何工作的。

# 链接

**视频指南:**[https://youtu.be/F0xD1DK3pe4](https://youtu.be/F0xD1DK3pe4)

**样板:【https://github.com/gary-george/nft-react-boilerplate】T22**

**Crypto Ghoulz:**https://cryptoghoulz.com/

**博文:**

**第 1 部分，共 3 部分:**[https://medium . com/coin monks/guide-to-creating-your-own-NFT-with-JavaScript-solidity-Part-1-of-3-7909 b 80 FAE 94](/coinmonks/guide-to-creating-your-own-nft-with-javascript-solidity-part-1-of-3-7909b80fae94)

**第 2 部分，共 3 部分:**[https://medium . com/coin monks/guide-to-creating-your-own-NFT-with-JavaScript-solidity-Part-2-of-3-CEA a1 CB 2412 a](/coinmonks/guide-to-creating-your-own-nft-with-javascript-solidity-part-2-of-3-ceaa1cb2412a)

**第 3 部分，共 3 部分:**[https://medium . com/coin monks/guide-to-creating-your-own-NFT-with-JavaScript-solidity-Part-3-of-3-6 da E8 da 7 e3f](/coinmonks/guide-to-creating-your-own-nft-with-javascript-solidity-part-3-of-3-6dae8da7e3f)

# 接下来是什么？

在第 2 部分中，我们将与我们的新 NFT 合同互动，并讨论如何上传您的资产准备铸造。

点击 [**【此处】**](https://garygeorge84.medium.com/guide-to-creating-your-own-nft-with-javascript-solidity-part-2-of-3-ceaa1cb2412a) 进行第二部分。

> 加入 [Coinmonks 电报频道](https://t.me/coincodecap)，了解加密交易和投资。

**还有，读**

*   [尤霍德勒 vs 科恩洛安 vs 霍德诺特](/coinmonks/youhodler-vs-coinloan-vs-hodlnaut-b1050acde55a) | [Cryptohopper vs 哈斯博特](https://blog.coincodecap.com/cryptohopper-vs-haasbot)
*   [币安 vs 北海巨妖](https://blog.coincodecap.com/binance-vs-kraken) | [美元成本平均交易机器人](https://blog.coincodecap.com/pionex-dca-bot)
*   [如何在印度购买比特币？](/coinmonks/buy-bitcoin-in-india-feb50ddfef94) | [WazirX 审核](/coinmonks/wazirx-review-5c811b074f5b) | [BitMEX 审核](https://blog.coincodecap.com/bitmex-review)
*   [比特币主根](https://blog.coincodecap.com/bitcoin-taproot) | [Bitso 评论](https://blog.coincodecap.com/bitso-review) | [排名前 6 的比特币信用卡](/coinmonks/bitcoin-credit-card-bc8ab6f377c6)
*   [双子座 vs 比特币基地](https://blog.coincodecap.com/gemini-vs-coinbase) | [比特币基地 vs 北海巨妖](https://blog.coincodecap.com/kraken-vs-coinbase) | [硬币罐 vs 硬币点](https://blog.coincodecap.com/coinspot-vs-coinjar)
*   [印度加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9) | [比特币储蓄账户](/coinmonks/bitcoin-savings-account-e65b13f92451) | [Paxful 审核](/coinmonks/paxful-review-4daf2354ab70)
*   [杠杆令牌](/coinmonks/leveraged-token-3f5257808b22) | [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [AscendEX 评论](/coinmonks/ascendex-review-53e829cf75fa)
*   [Godex.io 审核](/coinmonks/godex-io-review-7366086519fb) | [邀请审核](/coinmonks/invity-review-70f3030c0502) | [BitForex 审核](https://blog.coincodecap.com/bitforex-review) | [HitBTC 审核](/coinmonks/hitbtc-review-c5143c5d53c2)
*   [Crypto.com 费用](/coinmonks/binance-fees-8588ec17965) | [Botcrypto 审查](/coinmonks/botcrypto-review-2021-build-your-own-trading-bot-coincodecap-6b8332d736c7) | [替代方案](https://blog.coincodecap.com/crypto-com-alternatives)
*   [有哪些交易信号？](https://blog.coincodecap.com/trading-signal) | [Bitstamp vs 比特币基地](https://blog.coincodecap.com/bitstamp-coinbase)
*   [ProfitFarmers 回顾](https://blog.coincodecap.com/profitfarmers-review) | [如何使用 Cornix Trading Bot](https://blog.coincodecap.com/cornix-trading-bot)
*   [MXC 交易所评论](/coinmonks/mxc-exchange-review-3af0ec1cba8c) | [Pionex vs 币安](https://blog.coincodecap.com/pionex-vs-binance) | [Pionex 套利机器人](https://blog.coincodecap.com/pionex-arbitrage-bot)
*   [我的密码交易经验](/coinmonks/my-experience-with-crypto-copy-trading-d6feb2ce3ac5) | [比特币基地评论](/coinmonks/coinbase-review-6ef4e0f56064)
*   [CoinFLEX 评论](https://blog.coincodecap.com/coinflex-review) | [AEX 交易所评论](https://blog.coincodecap.com/aex-exchange-review) | [UPbit 评论](https://blog.coincodecap.com/upbit-review)