# 区块链开发环境

> 原文：<https://medium.com/coinmonks/blockchain-development-environment-775176fd88f3?source=collection_archive---------0----------------------->

![](img/c4e3fa5436812eb800b2a3c9de508765.png)

[source](http://chimoosoft.com/local-development-environment/)

*以下说明假设 Ubuntu 16.04 linux，但对于其他版本的 linux(和 MacOS)也类似。通过初始化一个* [*AWS EC2 实例*](https://aws.amazon.com/ec2/) *，可以从头开始创建一个 Ubuntu 服务器。请注意，免费的 t2.micro 没有足够的资源来运行区块链。相反，至少创建一个附加了 16GiB 卷的 t2.medium 实例。参见* [*此处*](/@PrateeshNanada/steps-to-install-testrpc-in-windows-10-96989a6cd594) *了解 Windows 10 安装的基本说明。*

# 软件包安装

确保包管理器引用了以太坊库，并且在基本以太坊开发包中是最新的

```
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get -y upgradesudo apt-get -y install curl git vim build-essential libssl-dev
sudo apt-get install software-properties-common
```

然后为 Javascript 运行时环境安装 Node.js(最好使用 NVM):

```
curl -o- [https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh](https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh) | bashsource ~/.profile
nvm install node
```

使用 Node.js 包管理器安装 Truffle(以太坊开发框架)和 Solidity(智能合约的高级语言)

```
npm install -g truffle
npm install -g solc
```

安装用于测试和开发的个人区块链 RPC 客户端(以前被称为 testrpc，但已被重命名为 Ganache)

```
npm install -g ganache-cli
```

这些**可选的**安装将来可能对其他开发工具有用，但是可以以后安装。

```
sudo apt-get install ethereum ##*optional, installs geth* sudo apt-get install solc *##optional, installs C-version of solidity* npm install web3 ethereumjs-util ethereumjs-tx *###optional*
sudo npm install -g express *###optional* sudo npm install web3-utils ###optional utilities
```

# 测试安装

用一个示例 Truffle 项目测试安装，比如 Truffle 发行版附带的 MetaCoin

```
mkdir MetaCoin
cd MetaCoin
truffle unbox metacoin
truffle compile
truffle develop
```

这将启动一个简单的测试网络和命令行界面。它还创建了 10 个虚拟用户帐户，每个帐户有 100 个虚拟以太网。例如，在 Truffle 命令行界面中键入这个命令会显示第一个帐户的初始余额。

```
truffle(development)> web3.eth.getAccounts().then(function(accounts) {return web3.eth.getBalance(accounts[0]);}).then(function(balance) {return web3.utils.fromWei(balance,'ether');}).then(function(ether) {return parseFloat(ether);})
**100**
```

可以使用 migrate 命令将合同部署到(虚拟)区块链中。注意这使用了 Ether，这可以通过在运行 migrate 后检查默认帐户上的余额来确认。

```
truffle(development)> migrate --reset
truffle(development)> web3.eth.getAccounts().then(function(accounts) {return web3.eth.getBalance(accounts[0]);}).then(function(balance) {return web3.utils.fromWei(balance,'ether');}).then(function(ether) {return parseFloat(ether);})
**99.9229097**
```

使用 Ctrl-D 退出块菌。

# 迁移到独立网络

Truffle `develop`选项提供了一种快速调试合同的方法，但是它只包含在开发计算机上。任何网络测试和计算节点之间的测试都需要实际的网络。

一个好的第一步是在开发计算机上测试网络。在另一个终端中启动测试网络(ganache-cli )(首选，因为各种日志将会回显到屏幕上),或者在后台用&符号运行

```
ganache-cli&
```

需要对 Truffle 进行配置才能实现这一点，编辑元币目录中的 truffle.js，如下所示:

```
module.exports = {
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8545,
      network_id: "*" // Match any network id
    }
  }
};
```

然后运行松露，迁移合同。这将产生与使用`truffle develop`相同的结果

```
truffle console
truffle(development)> migrate --reset
```

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)