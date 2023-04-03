# 使用 0 ETH: LOOM 部署智能合约

> 原文：<https://medium.com/coinmonks/deploy-smart-contracts-with-0-eth-loom-c59cc54b749?source=collection_archive---------2----------------------->

是的，你没听错。这不是一篇点击诱饵的文章，我将告诉你如何从零开始部署你的智能合同，费用为零。

走在前面之前，先试着理解什么是织机？

来源— [织机](https://loomx.io/developers/en/intro-to-loom.html)

> “Loom Network 是面向严肃的 dapp 开发人员的生产就绪型多链互操作平台。它为开发人员提供了开发高性能 dappss 所需的工具和知识，这些 dapp 需要跨多个链的快速无缝 UX。通过与比特币、以太坊、币安链和所有主要区块链的集成，部署一次到 Loom 可以让您的 dapp 通过达到当今最大可能的用户群来适应未来。”

![](img/83e5ece37dc1f92127881c4921500876.png)

loom blockchain

本文主要关注技术方面以及如何在 Loom 上从头开始部署契约，要了解更多理论知识，您可以随时访问 Loom 文档。

不用浪费太多时间，我们开始吧。

1.  **下载织机二进制。**

```
curl https://raw.githubusercontent.com/loomnetwork/loom-sdk-documentation/master/scripts/get_loom.sh | sh
# set LOOM_BIN to reference the downloaded loom binary,
# makes it easy to invoke it from anywhere
export LOOM_BIN=`pwd`/loom
```

2.**为织机生成私钥和公钥。**

```
$LOOM_BIN genkey -k extdev_private_key -a extdev_public_key
```

3.安装并设置 Truffle。

我们将使用块菌来建立我们的结构。

```
$ npm install -g truffle
```

现在我们将初始化松露

```
$ truffle init
Downloading...
Unpacking...
Setting up...
Unbox successful. Sweet!Commands: Compile:        truffle compile
  Migrate:        truffle migrate
  Test contracts: truffle test
```

到目前为止，我们已经创建了一个 Loom 和 Truffle 的原始结构，现在我们需要做的就是编写一个简单的智能合同，并选择一个要部署的网络。因为这是为了测试目的，所以我们在这里部署在 **extdev-plasma-us1 上。**

4.**写一个简单的智能合同-**

在这里，我只是给你看一个演示，你可以用 n 的复杂度来写你的智能合约。

我已经为柜台写了一个简单的智能合同。

```
pragma solidity ^0.5.8;contract Counter {
  uint counter;constructor() public {
    counter = 0; // Initialise the counter to 0
  }function increment() public {
    counter++;
  }function getCounter() public view returns (uint) {
    return counter;
  }
}
```

这个你得保存在/contracts/counter.sol 上

我为你提供这个项目的 Github 回购链接，供进一步参考，你可以查看。

我们还需要修改迁移文件。

5.**安装所需的依赖项。**

我们需要为 Truffle HD wallet 安装 Loom 支持，这样我们就可以与它连接。

```
$ npm install truffle-hdwallet-provider loom-truffle-provider dotenv --save-dev
```

6.**设置 truffle_config.js 文件。**

现在我们几乎完成了，我们需要做最关键的部分，我们需要设置 truffle 配置文件，并定义在 Loom 上部署它所需的所有配置。

```
require('dotenv').config();
const LoomTruffleProvider = require('loom-truffle-provider');
const HDWalletProvider = require('truffle-hdwallet-provider');module.exports = {
  networks: {
    extdev: {
      provider: function() {
          const privateKey = process.env.PRIVATE_KEY;
          const chainId = 'extdev-plasma-us1';
          const writeUrl = '[http://extdev-plasma-us1.dappchains.com:80/rpc'](http://extdev-plasma-us1.dappchains.com:80/rpc');
          const readUrl = '[http://extdev-plasma-us1.dappchains.com:80/query'](http://extdev-plasma-us1.dappchains.com:80/query');;
          return new LoomTruffleProvider(chainId, writeUrl, readUrl, privateKey)
      },
      network_id: '9545242630824'
    }
  }
```

这里你可以注意到我们使用 chainId 作为 **extdev-plasma-us1** ，因为我们在 testnet 上部署它，如果你需要在生产上部署它，你可以改变它。

此外，如果您还记得，我们已经为织机生成了一个私有和公共密钥。我们正在使用它，你可以做一个。env 文件并定义 PRIVATE_KEY，这样它就可以通过这个。

瞧吧！！现在我们都设置好了，现在我们可以编译和迁移它了。

在当前目录中打开一个终端。

然后跑

```
truffle compiletruffle migrate --network extdev
```

您将在终端上看到类似这样的内容。

```
Compiling your contracts...
===========================
> Everything is up to date, there is nothing to compile. Starting migrations...
======================
> Network name:    'extdev'
> Network id:      9545242630824
> Block gas limit: 0x0 1_initial_migration.js
====================== Deploying 'Migrations'
   ----------------------
   > transaction hash:    0xbfc8761ec98ee6940773cfb9c04c84dc1069fb20e66e8bf7c5dbc6a3c1198fd7
   > Blocks: 0            Seconds: 0
   > contract address:    0xEeb7A3C57DA6DdBa60F0a053Dbf4Cf1467313487
   > block number:        8084445
   > block timestamp:     1567489492
   > account:             0xEF6E82E8099Db14bB0a7cD426aE18ca0169Ee767
   > balance:             0
   > gas used:            0
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH 2_deploy_contracts.js
===================== Deploying 'Counter'
   -------------------
   > transaction hash:    0x0d67c1eee590940e6fe72bcfce494bc562c6d5152630fda27c45b09aaab1bfa8
   > Blocks: 0            Seconds: 0
   > contract address:    0xcB5B46FA01Ce20a69AE972AE6A983205d896be1b
   > block number:        8084452
   > block timestamp:     1567489505
   > account:             0xEF6E82E8099Db14bB0a7cD426aE18ca0169Ee767
   > balance:             0
   > gas used:            0
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH Summary
=======
> Total deployments:   2
> Final cost:          0 ETH
```

恭喜你！！！您已经成功部署了与 0 ETH 的 smart 合同，您可以在最终成本中看到这一点。

现在你可以给我发邮件了。

所以，这都是我这边的。你可以在下面找到 Github 的链接，也可以让我知道下面的任何问题或反馈。

谢谢你

区块链快乐。

[Github](https://github.com/pranshurastogi/Loom-dapp-scratch)——[Linkedin](https://www.linkedin.com/in/rastogipranshu/)——[Twitter](https://twitter.com/pranshu3196)

[![](img/673b3528e0b341cca62769c9baa632cd.png)](https://coincodecap.com)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)