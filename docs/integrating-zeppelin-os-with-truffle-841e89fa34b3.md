# 将齐柏林飞艇操作系统与松露相结合

> 原文：<https://medium.com/coinmonks/integrating-zeppelin-os-with-truffle-841e89fa34b3?source=collection_archive---------6----------------------->

![](img/f5dfd3eb68684f6e23bbcc7d6006d1bb.png)

*tldr；使用迁移模式和齐柏林操作系统到松露的合并命令，轻松地将齐柏林操作系统集成到现有的基于松露的项目中。您可以在* [*Github*](https://github.com/asselstine/zos-truffle-migrations-tutorial) 上找到教程和示例代码的镜像

[齐柏林飞艇操作系统](https://zeppelinos.org/)是一个管理合同部署、升级和打包的工具。它甚至聪明地检测并警告可升级合同中的内存结构变化。只需很少的努力，您就可以使您的所有合同都可以升级，并享受一个易于使用的命令行工具来管理它们。这很重要。

ZOS 旨在与[松露](https://truffleframework.com/)融合；一套备受推崇的工具，允许人们轻松迁移、部署和测试智能合同。

# 迁移

ZOS 负责合同升级，但没有直接支持松露迁移。迁移很有价值，因为它们以离散的版本化步骤捕获部署逻辑。开发人员可以添加新的迁移并增量迁移他们的环境。

要将块菌迁移与齐柏林飞艇操作系统集成，我们需要解决两个主要问题:

1.  必须由单独的“管理员”帐户使用齐柏林操作系统实例化合同。齐柏林操作系统将合同“管理员”和合同“用户”的角色分开:管理员是创建和升级目标合同的人，用户是调用原始合同功能的任何人。齐柏林飞艇称之为[透明代理模式](https://blog.zeppelinos.org/the-transparent-proxy-pattern/)。
2.  块菌迁移依赖于部署的迁移合同中存储的版本。如果重新创建构建工件，我们需要将`zos.*.json`文件中的地址合并到块菌工件中。

让我们来完成这两个步骤。

# 项目设置

完整的代码在 [Github](https://github.com/asselstine/zos-truffle-migrations-tutorial) 上，但是如果你想跟着做，你可以运行这些命令来设置你的项目:

在单独的终端启动 ganache-cli:

现在你可以跟着文章走了。

# 将合同创建与特权交互分离开来

许多合同将特殊地址定义为“所有者”。合同的所有者能够使用特权管理功能:例如 ERC20 合同上的`mint`。ZOS 不允许代理管理员与合同进行交互，因此所有者不能部署合同。我们需要调整我们的迁移，以便单独的“代理管理员”帐户实际部署合同。

拥有多个交易签名账户带来了一个额外的问题:我们现在需要在 Truffle 网络配置中‘解锁’多个账户。对于我们本地的`ganache-cli`来说，这不成问题，因为前十个帐户在 ganache-cli 中是解锁的，但是对于 Ropsten 这样的测试网络，我们需要手动解锁它们。

# Truffle 网络配置

[truffle-HD wallet-provider](https://github.com/trufflesuite/truffle-hdwallet-provider)允许使用一个偏移量来解锁多个地址，并将其作为第三和第四个参数:

我们已经解锁了`address[0]`和`address[1]`。因为 Truffle 使用第一个地址来更新迁移版本，所以让我们使用第二个地址作为代理管理员。

# 迁移. sol

我们要更新的第一个迁移将是每个块菌项目附带的`1_initial_migration.js`。它部署了 Truffle 进行迁移版本控制所依赖的迁移契约。`Migrations.sol`合同需要更新以与 ZOS 兼容。注意，我们正在使用来自`[openzeppelin-eth](https://github.com/OpenZeppelin/openzeppelin-eth)`模块的`Ownable.sol`，它是与 ZOS 集成的 OpenZeppelin 库的一个特殊分支。

接下来我们需要更新`1_initial_migration.js`脚本来使用 ZOS。我更喜欢使用 shell 命令，但是 ZOS 确实提供了 JavaScript API。

请注意，我们使用带有`accounts[0]`的继承函数`initialize(address _owner)`来初始化契约。

# CallMeMaybe.sol

为了使画面完整，让我们在迁移中需要调用函数的地方添加一个任意契约:

让我们为`CallMeMaybe`添加两个迁移，第一个创建它，第二个设置它(任意示例)。

这演示了与 Truffle 的集成:我们可以像平常一样简单地引入契约构件并调用它们。

合同和迁移都完成了！现在让我们运行它们。

# 运行迁移

假设`accounts[0]`(所有者)是**0x 2c 1e 88 eeb 8 a1 aa 907 dcaa 141 f8a 930565637 ff 57**并且`accounts[1]`(代理管理员)是**0x 81 ff 0179 eeb 3545 e 7d 9 A0 e 80672 ea 9 ba 88d 68817**现在让我们部署我们的合同:

使用 Truffle 控制台检查名称:

# 将地址合并回块菌工件:zos-块菌-合并

当松露建造神器被破坏并重新创建时，它们会丢失来自齐柏林飞船的所有地址信息。要将地址信息合并回文件中，你可以使用一个我写的名为 [zos-truffle-merge](https://github.com/MedXProtocol/zos-truffle-merge) 的便利工具。

要将 Ropsten 中的 ZOS 地址合并回工件:

这将把在`zos.ropsten.json`中定义的已部署的契约地址合并到`build/contracts`中用于网络 id `3`的块菌工件中。

Zeppelin OS 已经将该功能添加到他们的 [backlog](https://github.com/zeppelinos/zos/issues/420) 中，因此该命令最终可能会被内置功能所取代。

# 摘要

我非常喜欢与 Zeppelin OS 和 Truffle 一起工作；这两个工具是一个强大的组合。如果你是一个松露用户，我希望这篇文章能帮助你过渡到使用 Zeppelin OS，因为我认为人们可以从中受益匪浅。

中:[布伦丹·阿塞尔斯汀](https://medium.com/u/450060e6b469?source=post_page-----841e89fa34b3--------------------------------)

github:[https://github.com/asselstine](https://github.com/asselstine)

推特: [@b_asselstine](https://twitter.com/b_asselstine)

[![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)

[**Click to read today’s top story**](http://bit.ly/2G71Sp7)