# 使用 Hardhat & Truffle 的项目设置—第 3 部分

> 原文：<https://medium.com/coinmonks/project-setup-using-hardhat-truffle-part-3-724ab0d2a1fe?source=collection_archive---------2----------------------->

![](img/a46857fbcc24f3f64a30c061a023ea6c.png)

现在，我们将编写一个简单的智能合约，并将其部署到我们本地的区块链

# **撰写智能合同**

> 我们可以暂时忽略现有的合同，但可以自由探索它们

对于 Truffle 和 Hardhat，我们可以在合同文件夹中创建一个新文件

> 在我们的例子中，我们将使用可靠的语言。更多信息可以在[这里](https://docs.soliditylang.org/en/v0.8.7/)找到

*   将其命名

```
NumberChanger.sol
```

*   声明 SPDX-License，并将其注释掉

```
// SPDX-License-Identifier: MIT
```

*   定义合同将遵守的 Solidity 版本，我们将把它设置为与我们的配置文件中相同的版本

> 这使得它可以用 0.8.6 或更高版本进行编译

```
pragma solidity ^0.8.6;
```

*   编写我们的契约，首先声明它，并赋予它与我们的文件名相同的名称

```
/ SPDX-License-Identifier: MITpragma solidity ^0.8.6; contract NumberChanger {}
```

*   声明一个名为 number 的状态变量

```
// SPDX-License-Identifier: MITpragma solidity ^0.8.6; contract NumberChanger {uint256 number;}
```

*   创建一个函数来检索存储在状态变量中的数字

```
// SPDX-License-Identifier: MITpragma solidity ^0.8.6;contract NumberChanger {uint256 number;function getNumber() public views returns(uint256) {return number; }}
```

*   创建一个函数来更新状态变量中的数字

```
// SPDX-License-Identifier: MITpragma solidity ^0.8.6;contract NumberChanger {uint256 number;function getNumber() public views returns(uint256) {return number;}function setNumber(uint256 _number) public {number = _number; }}
```

我们现在已经编写了一个智能合同，允许在区块链上存储和更新号码

# **安全帽**

为了做到这一点，Hardhat 允许控制台登录选项

*   声明 Solidity 版本后，导入 hardhat/console.sol

```
import "hardhat/console.sol"// SPDX-License-Identifier: MITpragma solidity ^0.8.6;import "hardhat/console.sol"
```

*   将它添加到您希望看到输出的位置

```
// SPDX-License-Identifier: MITpragma solidity ^0.8.6;import "hardhat/console.sol";contract NumberChanger {uint256 number;function getNumber() public view returns(uint256) {return number;}function setNumber(uint256 _number) public {number = _number;console.log(number) }}
```

> 只有在使用 hardhat 时，hardhat/console.sol 的导入和 console.log 的使用才是可选的

完成的合同应该是这样的

```
// SPDX-License-Identifier: MITpragma solidity ^0.8.6;contract NumberChanger {uint256 number;function getNumber() public view returns(uint256) {return number;}function setNumber(uint256 _number) public {number = _number; }}
```

## 在第 4 部分中，我们将把我们的合同部署到当地的区块链。 [**点击此处查看第四部分**](/@shihyuhwang/9d2f55842be2) **。**

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资

## 另外，阅读

*   [非洲最佳加密交易所](https://blog.coincodecap.com/crypto-exchange-africa) | [胡交易所评论](https://blog.coincodecap.com/hoo-exchange-review)
*   [eToro vs robin hood](https://blog.coincodecap.com/etoro-robinhood)|[MoonXBT vs by bit vs Bityard](https://blog.coincodecap.com/bybit-bityard-moonxbt)
*   [有哪些交易信号？](https://blog.coincodecap.com/trading-signal) | [比特斯坦普 vs 比特币基地](https://blog.coincodecap.com/bitstamp-coinbase)
*   [ProfitFarmers 回顾](https://blog.coincodecap.com/profitfarmers-review) | [如何使用 Cornix 交易机器人](https://blog.coincodecap.com/cornix-trading-bot)
*   [如何在势不可挡的域名上购买域名？](https://blog.coincodecap.com/buy-domain-on-unstoppable-domains)
*   [印度的加密税](https://blog.coincodecap.com/crypto-tax-india) | [altFINS 审查](https://blog.coincodecap.com/altfins-review) | [Prokey 审查](/coinmonks/prokey-review-26611173c13c)
*   [Blockfi vs 比特币基地](https://blog.coincodecap.com/blockfi-vs-coinbase) | [BitKan 点评](https://blog.coincodecap.com/bitkan-review) | [Bexplus 点评](https://blog.coincodecap.com/bexplus-review)
*   [南非的加密交易所](https://blog.coincodecap.com/crypto-exchanges-in-south-africa) | [BitMEX 加密信号](https://blog.coincodecap.com/bitmex-crypto-signals)
*   [MoonXBT 副本交易](https://blog.coincodecap.com/moonxbt-copy-trading) | [阿联酋的加密钱包](https://blog.coincodecap.com/crypto-wallets-in-uae)
*   [雷米塔诺审查](https://blog.coincodecap.com/remitano-review)|[1 英寸协议指南](https://blog.coincodecap.com/1inch)