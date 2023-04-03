# 使用 Openzeppelin CLI 升级您的智能合约[2020]

> 原文：<https://medium.com/coinmonks/upgrade-your-smart-contract-using-openzeppelin-cli-2020-4422fbd328cd?source=collection_archive---------2----------------------->

就在一个多月前，我实习的办公室给了我一项任务，让我把他们的一份智能合同“升级”。于是，开始了我的旅程。通过查阅 [Openzeppelin docs](https://docs.openzeppelin.com/upgrades/2.8/writing-upgradeable) 、[论坛](https://forum.openzeppelin.com/)和众多互联网文章，我得以完成这个任务。我把它展示给了我的办公室，现在是时候和你们大家分享了！我试图用尽可能少的附带信息使这成为简单明了的代码指南。顺便说一下，您需要对以太坊上的基本智能合约开发有所了解，如果您……拿起一杯咖啡，加入进来吧！💁

可升级性是区块链的一个特性，宣传的不如实现的多。哪里…？在几乎所有部署到 mainnet 的智能合同中！嗯…为什么不呢？到处都需要可升级性！对区块链来说更是如此，他们处理敏感数据和资产的移动，包括金钱！如果包含一些错误或功能障碍的智能合同落入贪婪黑客的邪恶视线，可能会发生不可逆转的灾难性损失(例如，查看这些[大黑客](/solidified/the-biggest-smart-contract-hacks-in-history-or-how-to-endanger-up-to-us-2-2-billion-d5a72961d15d))。在升级过程中，您可以修复现有智能合同中的错误、漏洞并添加功能和变量，而无需更改您和您的用户在区块链中与之交互的地址。所有这些都是在升级之间保持变量的值或状态不变的情况下发生的！现在你可能会问…这是怎么回事？“不可改变的”区块链发生了什么事！？！

嗯…放松！在你开始质疑区块链的诚信之前，让我告诉你…它们仍然是不可改变的！你不知道的是这个被称为[代理模式](https://docs.openzeppelin.com/upgrades/2.6/proxies)的绝妙主意，它让区块链保持其本性，同时能够前进并适应变化！该模式通过创建一个包装契约(代理)来实现这一点，该包装契约将调用委托(转发)给我们的实际实现契约，同时用实现智能契约的状态变量来映射自身中的存储槽。对于每个事务，从代理使用状态存储，而从实现契约应用功能逻辑。因此，当一个实现契约被更新时，整个契约被部署到代理将指向下一个的另一个地址…给我们一种错觉，好像我们的契约是从以前的状态“更新”的！

自己实现起来相当复杂。这就是为什么我们使用像 [Openzeppelin CLI](https://docs.openzeppelin.com/cli/2.7/) 这样的工具，让我们使用非常简单的命令来完成所有这些工作，同时从我们那里抽象出事后诸葛亮的沉重细节！

我们将在本文中讨论以下主题。设置 Openzeppelin CLI
2。添加配置文件
3。撰写可升级智能合同时需要考虑的要点
4。示例合同:简单存储 5。编译和部署智能合同
6。升级到版本 2…增加功能和状态
7。将版本 2 部署到网络
8。与升级后的实例
9 进行交互。使用库升级到版本 3…10。重新交互以重新检查

> 注 *:* 您可以在本回购 **中找到本指南 [**的代码；)**](https://github.com/asmitadhungana/UpgradeSmartContracts)**

# 1.设置 Openzeppelin CLI

为了能够使用 openzeppelin 的内置功能(它很健壮，可以与 truffle 相媲美)，您首先需要使用它的 CLI。首先，确保您的计算机中全局安装了 nodeJS 和 npm。现在，在你想要的驱动器中创建一个新目录，然后跳进去！一旦进入:

*   初始化一个空节点项目
    `$ npm init`
*   安装 Openzeppelin CLI
    
*   创建一个新的 CLI 项目来管理您已部署的合同
    `$ npx openzeppelin init`*【注意:您可以将每个 CLI 命令的“openzeppelin”替换为“oz”】* 在初始化时，将形成三个主要构件:
    **。打开 zeppelin* 目录，该目录将保存项目特定信息
    ** contracts* 目录，您将在其中编写所有智能 contracts
    ** networks . js*文件，您将在其中管理您的网络配置
*   注意跟踪*中的以下文件。openzeppelin* 使用版本控制(git)
    **project . JSON* *公共网络文件像*mainnet.json/ropsten.json/rinkeby.json*

# 2.添加配置文件

您可以将这些内容复制到项目中各自的文件中:

*   **package.json**

fig 1: package.json

现在…运行`$ npm install`
在这里，第 13 行、第 14 行和第 19 行中的依赖项是主要的依赖项[阅读 3.3]，而其他的是可选的。它们是 openzeppelin 库，允许我们创建可升级的智能合同。在 15 中，依赖项是***Truffle HD wallet provider***，一个通过 [infura.io](https://infura.io/) 或任何其他兼容提供程序配置以太坊网络连接的便捷工具。而第 16 行的 ***dotenv*** 允许你从源代码中分离出秘密。这在协作环境中很有用，在这种环境中，您可能不想与他人共享您的数据库登录凭证或私钥。

*   **networks.js**

fig 2: networks.js

这些是允许您的 openzeppelin 项目连接到各自网络的网络配置。这里，我们为本地以太坊模拟器 [Ganache](https://www.trufflesuite.com/ganache) 和以太坊测试网 [rinkeby](https://rinkeby.etherscan.io/) 添加网络配置(将在本教程中使用)。

## 。包封/包围（动词 envelop 的简写）

fig 3: .env

添加来自你的 metamask 钱包账户的助记符(确保它加载了一些测试乙醚)和你的 rinkeby 的 Infura 密钥，你可以按照[这篇文章](https://walkingtree.tech/deploying-a-smart-contract-in-rinkeby-using-infura/)生成！

> **警告！！！您不应将此文件提交给版本控制，因为它包含您的 wallet 种子短语和您的 API 密钥；有机器人等着用乙醚从任何账户中清除资金！为避免这种情况，请添加"。env "在你的**中换一行。gitignore****

# 3.编写可升级智能合同时要考虑的要点

这些限制是由于管理 EVM 可升级智能合同开发的代理模式而产生的。这些是由于 EVM 的限制，而不是 openzeppelin 的限制:

1.  **No constructor()函数:** 您不能使用 solidity 构造函数来初始化您的设置逻辑。相反，使用 initialize 函数代替带有初始化器修饰符的构造函数，初始化器修饰符继承自 open zeppelin/contracts-ether eum-package/contracts/initialize able . sol。
2.  **无初始值声明:** 变量在契约中声明时不能声明初始值。`eg: uint256 initialValue = 0;` 不允许出现在函数体之外。相反，在*初始化器中为你的契约定义初始值。*
3.  没有普通的合同和库作为依赖关系
    确保你安装的是`[@openzeppelin/contracts-ethereum-package](https://github.com/OpenZeppelin/openzeppelin-contracts-ethereum-package)`而不是普通的`[@openzeppelin/contracts](https://github.com/OpenZeppelin/openzeppelin-contracts)`。后者是为一般用途而设置的，而`@openzeppelin/contracts-ethereum-package`是为与 [OpenZeppelin 升级](https://docs.openzeppelin.com/upgrades/2.8/)一起使用而定制的。这意味着其合同[已经设置为可升级](https://docs.openzeppelin.com/cli/2.6/dependencies#upgrades::writing-contracts.adoc#use-upgradeable-packages)。
4.  **升级期间无存储布局干预** 升级期间您不能:
    a .更改现有变量的类型
    b .更改变量声明的顺序
    c .删除现有变量
    d .在现有变量之前引入新变量

# 4.合同示例:简单存储

在“contracts”文件夹中，让我们在 Solidity 中创建一个名为“SimpleStorage.sol”的普通智能合同。它有一个名为 *storedValue* 的状态变量和一个函数 *retrieve()* ，任何用户都可以通过它来检索 *storedValue* [ *注意，solidity 将默认为公共状态变量提供一个 getter 函数，这只是为了演示的目的*

fig 4: vanilla Unupgradeable SimpleStorage.sol

现在，为了使它可升级，您必须按照[3]中的规则做一些调整，如下所示:

fig 5: modified Upgradeable SimpleStorage.sol

# 5.编译和部署智能合同

使用 openzeppelin cli 命令来完成，如下所示:

1.  编制合同:
    `**$ npx oz compile**`
2.  将其部署到网络:
    `**$ npx oz deploy**`*这将带您到一个交互式控制台，在那里您应该根据您的要求选择给定的选项。因为我将它部署在 rinkeby testnet 中，所以我会这样做:*

```
? choose the kind of deployment: upgradeable
? Pick a network: rinkeby
? Pick a contract to deploy: SimpleStorage
? Call a function to initialize the instance after creating it? Y
? Select which function*:  *initialize()**If all goes well, the returned text should be similar to this:*✓ Setting everything up to create contract instances
✓ Instance created at 0xC9F4797BeAA8c56af23760e7EB57A6e7196c693a
To upgrade this instance run ‘oz upgrade’ 0xC9F4797BeAA8c56af23760e7EB57A6e7196c693a
```

# 6.升级到 v2…添加功能和状态

假设我们想要添加一个可以增加 storedValue 的值的功能。因此，在前面的契约中，让我们添加一个新的状态变量 *addedValue* 来存储传递的参数，并添加一个 *incrementValue( )* 函数来将其添加到 *storedValue。*

fig 6: SimpleStorage.sol Version 2

# 7.将版本 2 部署到网络

为此，您必须使用 CLI 命令“ *oz upgrade* ”，这将再次带您进入一个交互式控制台。选择如下所示的选项:

```
**$ npx oz upgrade**? Pick a network:  rinkeby
? Which instances would you like to upgrade?:  Choose by name
? Pick an instance to upgrade ?  SimpleStorage
? Call a function on the instance after upgrading it?  NoNew variable 'uint256 addedValue' was added in contract SimpleStorage in contracts/SimpleStorage.sol:1 at the end of the contract.
See https://docs.openzeppelin.com/upgrades/2.6//writing-upgradeable#modifying-your-contracts for more info.✓ Contract SimpleStorage deployed
All implementations have been deployed
✓ Instance upgraded at 0xC9F4797BeAA8c56af23760e7EB57A6e7196c693a. Transaction receipt: 0x43834b9b13c20db353b657fff15f4a3881da0cd092dc994cfb3400513e229d0f
✓ Instance at 0xC9F4797BeAA8c56af23760e7EB57A6e7196c693a upgraded
```

# 8.与升级后的(v2)实例交互

现在，让我们与升级后的实例进行交互，检查升级是否添加了 *increment()* 函数...
Openzeppelin 为我们提供了两个交互命令:
*oz send-tx*调用修改状态变量的函数和
*oz call*调用不修改状态变量的函数。

*   首先，我们将调用我们新引入的带有参数 *10* 的 *increment()* 函数来添加到 *storedValue* :

```
**$ npx oz send-tx**? Pick a network: rinkeby
? Pick an instance: SimpleStorage at 0xC9F4797BeAA8c56af23760e7EB57A6e7196c693a
? Select which function: incrementValue(x: uint256)
? x: uint256: 10
✓ Transaction successful. Transaction hash: 0x9a93f387a1166f324fa9221bc8d29abc948c97244f344c25fa237b786ce57f19
```

*   然后，我们将调用 *retrieve()* 函数来查看 *storedValue* 的值是否增加了:

```
**$ npx oz call**? Pick a network rinkeby
? Pick an instance SimpleStorage at 0xC9F4797BeAA8c56af23760e7EB57A6e7196c693a
? Select which function retrieve()
✓ Method ‘retrieve()’ returned: 10
10
```

我们可以看到， *retrieve()* 方法返回了 *10* 。也就是说我们的 *increment()* 函数添加成功了！干杯！

# 9。让我们再次升级…使用库:

现在，让我们看看如何将实用程序库添加到我们的可升级智能合同中。我们将把 [Openzeppelin 的 Safemath 库](https://github.com/OpenZeppelin/openzeppelin-contracts-ethereum-package/blob/master/contracts/math/SafeMath.sol)添加到我们的合同中，并修改 *increment()* 函数，以避免在添加过程中出现任何类型的溢出！请注意，我们只能添加可升级的库[3.3]

fig 7: SimpleStorage.sol Version 3

> **警告！**更改或添加父合同可能会更改您的合同的存储变量。

*这是基于:*[***https://docs . open zeppelin . com/upgrades/2.8/writing-upgradable # modifying-your-contracts***](https://docs.openzeppelin.com/upgrades/2.8/writing-upgradeable#modifying-your-contracts)

> 请注意，您也可能会通过更改其父合同无意中更改了合同的存储变量…交换基础合同的声明顺序，或引入新的基础合同，将会更改变量的实际存储方式…如果子合同有自己的变量，您也不能向基础合同添加新变量。

# 10.重新交互以重新检查:

现在，让我们将升级后的 SimpleStorage(版本 3)部署到 rinkeby testnet:

```
**$ npx oz upgrade**? Pick a network rinkeby
? Which instances would you like to upgrade? Choose by name
? Pick an instance to upgrade SimpleStorage
? Call a function on the instance after upgrading it? No
Nothing to compile, all contracts are up to date.
All implementations are up to date
✓ Instance upgraded at 0xC9F4797BeAA8c56af23760e7EB57A6e7196c693a. Transaction receipt: 0xe22a159bfaf3486a919db0d3cf66a5f199858f8cf4452efd2ef378a3ea07f1b8
✓ Instance at 0xC9F4797BeAA8c56af23760e7EB57A6e7196c693a upgraded
```

…并调用 *retrieve()* 函数来检查存储是否在这些升级之间被保留:

```
**$ npx oz call**? Pick a network rinkeby
? Pick an instance SimpleStorage at 0xC9F4797BeAA8c56af23760e7EB57A6e7196c693a
? Select which function retrieve()
✓ Method ‘retrieve()’ returned: 10
10
```

我们可以清楚地看到， *retrieve* 函数将 *storedValue* 的值返回为 *10* ，与上一版本保持不变。这样，我们可以修改以前的函数，添加库，修复错误，同时保持变量的状态不变。最棒的是…我们可以像以前一样和同一个地址互动！

这是本教程的全部内容…我希望这对你有用！在本系列的下一部分中，我将指导您如何以编程方式(使用我们自己的脚本)升级我们的智能合约！我将从 SimpleStorage smart 契约的最后一个状态(v3)继续向您展示！
敬请期待……；)

## **同样，请阅读**

*   [顶级 DeFi 项目](/coinmonks/defi-future-10-promising-projects-in-the-defi-world-ff2b697ab006)
*   [最佳加密交易机器人](/coinmonks/whats-the-best-crypto-trading-bot-in-2020-top-8-bitcoin-trading-bot-c16adeb13317)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   [unis WAP 最佳钱包](/coinmonks/best-wallets-to-use-uniswap-e91a6385d9e8)
*   最佳[加密贷款平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   Bitsgap 评论——一个轻松赚钱的加密交易机器人
*   为专业人士设计的加密交易机器人
*   Bitmex 的[保证金交易指南](/coinmonks/the-idiots-guide-to-margin-trading-on-bitmex-dbbd7742c6fc?source=friends_link&sk=7bfa99d2a181142510c8442c8ddb0786)
*   加密摇摆交易的权威指南
*   [Bitmex 高级保证金交易指南](/coinmonks/bitmex-advanced-margin-trading-guide-2270c195ce25?source=friends_link&sk=1d986cca731f5084b9a2db4a4bc4a7ad)
*   [面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   顶级[比特币节点](https://blog.coincodecap.com/bitcoin-node-solutions)提供商

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com?utm_source=coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://coincodecap.com?utm_source=coinmonks)