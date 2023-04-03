# 你的第一个 EOS dApp——合同

> 原文：<https://medium.com/coinmonks/your-first-eos-dapp-the-contract-ce793f43d852?source=collection_archive---------2----------------------->

![](img/0d631cd3a85debf5c56bf7bcbece7fae.png)

[source](https://themerkle.com/eos-io-announces-major-updates-to-project/)

在我们的[上一篇文章](/coinmonks/your-first-eos-dapp-the-setup-dde55d3dcfb7)中，我们设置了独立的 EOS 链以及我们的钱包和账户。在本文中，我们将编写用于管理学校入学抽奖的合同。我们将学习核心 EOS 概念，如

*   如何创建我的 EOS 智能合同
*   什么是 ***web assembly*** 在 EOS 中有什么用途
*   我如何部署我的 EOS 合同并与之交互

## 设置

我建议使用类似于 [Visual Studio Code](https://code.visualstudio.com/) 的 IDE，但是你真的可以使用任何编辑器。让我们继续创建下面的文件和项目目录。

```
mkdir lottery && cd lottery
mkdir contracts && cd contracts
mkdir lottery && cd lottery
```

## 密码

在*彩票/合同/彩票/彩票. cpp* 路径下创建一个*彩票. cpp* 文件

## 编制

EOS 在 **Web Assembly** 上下了很大的赌注，并选择它作为执行合同的运行时环境。

> *Web Assembly 是微软、谷歌和苹果支持的新兴行业标准。该标准的目标是使在您的浏览器中运行不受信任的高性能代码成为可能。* [*Web Assembly 是一款游戏改变者*](/mozilla-tech/why-webassembly-is-a-game-changer-for-the-web-and-a-source-of-pride-for-mozilla-and-firefox-dda80e4c43cb) *，它将实现高性能的 Web 应用，如视频和图像编辑以及游戏。—丹·拉里默*

这里我们生成 web 程序集(。wast)从我们的。cpp 文件，这可以从我们在上一篇文章中的安装中获得。注意，如果你不在彩票/合同/彩票文件夹中，你需要调整你的路径。

```
eosiocpp -o Lottery.wast Lottery.cpp
```

ABI 对于大多数区块链项目来说都很常见，它本质上是一个描述调用者可用的智能契约的动作和属性的接口。请注意 Lottery.cpp 文件中的 *//@abi* 语法，它告诉我们的 *eosiocpp* 编译器要公开哪些成员以及以何种方式公开。

```
eosiocpp -g Lottery.abi Lottery.cpp
```

## 部署

如果您的 EOS 链尚未启动，您需要启动它。你可能也需要打开你的钱包。

**启动链条**

您需要从安装和构建 EOS 的目录中运行下面的命令。

*注意，我们正在运行* **dawn-v4.1.0** *，我们有一个额外的参数，允许我们查看我们的****print()****方法，并且历史插件已经将名称改为****history _ API _ plugin***

```
./build/programs/nodeos/nodeos -e -p eosio --plugin eosio::wallet_api_plugin --plugin eosio::chain_api_plugin --plugin eosio::history_api_plugin --contracts-console
```

**解锁钱包**

```
cleos wallet unlock -n lottery
```

输入您在上一篇文章中为*彩票*钱包保存的密码。

你在[上一篇文章](/coinmonks/your-first-eos-dapp-the-setup-dde55d3dcfb7)中的 lottery.code 账户应该仍然存在，但是如果你升级了链并且丢失了数据，你可以运行这个来重新创建它。

```
cleos create account eosio lottery.code <owner public key> <active public key>
```

**部署合同**

现在我们有了**。浪费了**和。 **abi** 文件使用运行链和解锁的钱包，我们可以部署此合同！*注意，该命令是从* ***彩票*** *根项目文件夹中运行的。*

```
cleos set contract lottery.code ./contracts/lottery ./contracts/lottery/Lottery.wast ./contracts/lottery/Lottery.abi
```

## **测试账户**

我们需要创建两个以上的帐户来运行此合同。

*   管理员——这个账户是为管理彩票的学校管理员开设的
*   **家长** —该账户供家长为其学生报名参加抽奖

请运行此处描述的[帐户创建过程，或查看下面的命令摘要。](/coinmonks/your-first-eos-dapp-the-setup-dde55d3dcfb7)

*对于每个账户:*

```
// Generate owner public/private keys
cleos create key// Import owner private key into wallet
cleos wallet import <owner private key hash> -n lottery// Generate active public/private keys
cleos create key// Import active private key into wallet
cleos wallet import <active private key hash> -n lottery// Create account
cleos create account eosio <account name> <owner public key> <active public key>
```

一旦您创建了 admin 和 parent 帐户，我们将能够在本文的后面运行测试脚本。

## **与合同互动**

既然契约已经部署到链中，我们就可以开始与它进行交互了。我们有三个面向用户的功能:

*   添加级别—我们需要添加级别和可用空缺(这应该只有主管帐户可以访问)
*   删除等级—我们需要删除等级级别(这应该只有主管帐户可以访问)
*   获取级别—获取级别和可用空缺(所有客户都应该可以访问)
*   添加学生—我们需要一种方法，让家长能够输入他们孩子的信息，以便让他们参加抽奖。这应该适用于所有帐户，但我们将为此创建一个父帐户。此时，家长只能输入一名学生。
*   移除学生-输入学生的家长帐户应该能够将学生从彩票中移除。
*   获取学生-输入学生或主管的家长帐户应该能够让学生检查他/她的彩票状态
*   运行彩票——管理员帐户应该能够运行所有等级的彩票

## 测试脚本

为了提高效率，可以运行下面的测试脚本来说明上面的需求。

*免责声明:除了要求调用者是授权帐户并拥有他们自己的插入数据之外，我无法探索合同上的权限。如果有人能告诉我锁定智能合同的某些行为的方向，那将会很有帮助。我能想到的唯一办法就是维护我自己的权限表？*

上面的测试在目前的形式下并不理想，但实际上在开发环境中没有一个是理想的。这个堆栈比 truffle 开发环境落后好几光年，但是我将在下一篇文章中探索使用 [Tokenika 的产品 EOS Factory](https://eosprojects.org/projects/p/eosfactory/) 来解决一个更真实的开发者流。也就是说，做一些困难的事情并对 EOS CLI 工具有一个基本的了解是没有错的。

## 解决纷争

在本地运行 EOS 链是相当具有挑战性的，并且会出现它处于无效状态的情况。每天早上我必须重置我的链，因为交易已经过期，无法使用。

```
rm -rf "~/Library/Application Support/eosio/nodeos/data"<eos dir>/build/programs/nodeos/nodeos -e -p eosio --plugin eosio::wallet_api_plugin --plugin eosio::chain_api_plugin --plugin eosio::history_api_plugin --contracts-console
```