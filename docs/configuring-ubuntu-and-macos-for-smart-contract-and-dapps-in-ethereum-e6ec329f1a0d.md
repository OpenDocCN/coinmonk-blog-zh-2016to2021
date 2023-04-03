# 在以太坊中为智能合约和 DApps 配置 Ubuntu 和 macOS

> 原文：<https://medium.com/coinmonks/configuring-ubuntu-and-macos-for-smart-contract-and-dapps-in-ethereum-e6ec329f1a0d?source=collection_archive---------0----------------------->

(葡萄牙语版本可在[这里](/@saulobrito/configurando-ubuntu-e-macos-para-desenvolvimento-de-smart-contracts-e-dapps-em-ethereum-e-react-9258a21daabf)获得。)

在开始编写分散化的应用程序(**dapp**)之前，我们需要使用必要的工具来设置环境。

我们在[区块链学院](http://blockchainacademy.com.br)培训期间经常遇到的一个问题是不同版本和操作系统之间的软件不一致。为了简化流程，我们创建了一个虚拟机映像，其中预配置了我们在整个课程中所需的所有内容。

在本文中，我将解释我们使用哪些工具，以及如何在 Ubuntu 和 macOS 中安装这些工具。我们还在文章末尾提供了下载 Ubuntu 预配置映像的链接。

# 工具

## 编辑、编辑和出版

虽然 Ethereum 中有几种[高级语言](https://en.wikipedia.org/wiki/High-level_programming_language)可用于编写 Smart Contract ( **SC** )，但 **Solidity** 是目前最常用的语言，而[推荐在大多数情况下使用](https://ethereum.stackexchange.com/questions/21375/why-did-serpent-die)。在发送到区块链之前，需要将代码编译成**字节码**，这才是 **EVM** 真正理解的。

我们选择了 [Atom](https://atom.io/) 作为源代码编辑器，而 [linter-solidity](https://atom.io/packages/linter-solidity) 作为林挺和颜色编辑器。

在编译和发布时，我们使用[块菌](http://truffleframework.com/)框架，这是 Consensys 开发的，我们也建议进行测试。

在培训期间，我们还使用了 [Remix](http://remix.ethereum.org) web IDE，它是由 Ethereum Foundation 开发的，开发周期非常短。程式码会自动编译，并可在浏览器中执行的简化测试 EVM 中发布。我的建议是:坚持使用 Remix，直到你发现你需要一个更完整的工具来协作、测试等等。

## 发展区块链

在以太坊主网络上发布你的合同将是你最后要做的事情——从字面上理解。在你确定你的合同没有漏洞之前，你将使用区块链运行你的机器、专用网络或公共测试网络。为了更快的开发周期，我建议使用本地区块链，直到您需要与其他人协作。

在我们的培训中，我们使用两个本地区块链:开发模式中的 **geth** 和 **Ganache** 。geth 是官方客户端之一，你可以用它连接任何以太坊网络。在开发模式中，它附带一个加载了 ETH 的解锁帐户，并运行一个修改版本的**工作证明**，以便在收到新交易时立即生成块，并创建一个加载了 ETH 的解锁帐户。

加纳切是区块链的简化版。虽然你不能用它做所有的事情，但我发现它对于合同出版和测试非常实用。它有两种风格: [UI](http://truffleframework.com/ganache/) 和 [CLI](https://github.com/trufflesuite/ganache-cli) (以前的 TestRPC)。

## DApp 浏览器

我们通常用来浏览“传统”网络的浏览器(Firefox、Chrome、Safari 等等)还没有为我们所说的 **Web 3.0** 做好准备。在这个新兴的去中心化网络中，浏览器应该能够连接到区块链网络并理解新的协议，例如 [**IPFS**](https://ipfs.io/) 。

所以，现在，我们有两种方式与 DApp 互动:使用浏览器扩展/插件或使用 DApp 浏览器。

[**MetaMask**](https://metamask.io/) 是一个管理你的钱包的扩展，允许你在任何以太坊网络中签署交易——公开的或私人的。安装和配置后，它会要求您确认是否直接从浏览器传输 ETH/token 或与合同交互。目前支持 Chrome、Firefox、Opera 和 [Brave](https://brave.com/) 。

以太坊基金会维护着一个名为 [**迷雾**](https://github.com/ethereum/mist) 的 DApp 浏览器，有两个不同的版本:完整版和简化版的**以太坊钱包**。不同之处在于，虽然完整版——像任何“普通”浏览器一样，允许你使用地址栏导航到任何你想去的网站，但以太坊钱包只运行一个应用程序，即[以太坊钱包应用](https://github.com/ethereum/meteor-dapp-wallet)(可在[wallet.ethereum.org](https://wallet.ethereum.org)获得)。

**警告**:不建议使用 Mist 在您不信任的网站中导航，因为它可能会被利用来窃取您的资金。

## Javascript 库

最后，要将事务发送到以太网，您的应用程序需要连接到一个节点。建立这种连接最常见的方式是使用 [**web3.js**](https://github.com/ethereum/web3.js/) 库，该库现在支持 **JSON RPC** 和 **WebSockets** 协议。

我们应该在项目中声明 **web3.js** 依赖项，而不是全局安装，所以我将跳过这一步，在以后的文章中讨论。

# 配置 Ubuntu 16.04.4

我们选择的版本是 16.04.4 64 位，可以在 BitTorrent [这里](http://releases.ubuntu.com/16.04/ubuntu-16.04.4-desktop-amd64.iso.torrent)找到。

## 节点和 Git

我们将要安装的一些工具要求我们首先设置 [**节点。JS**](https://nodejs.org/en/) 和 [**Git**](https://git-scm.com/) ，那么就从那里开始吧。

我建议通过 NVM 安装 Node，这允许我们在同一台机器上有许多不同的版本:

```
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
```

关闭并重新打开终端和安装节点:

```
nvm install 8.11.1
```

您可以使用以下命令检查它是否工作:

```
node --version
```

要安装 Git，我们将使用 Ubuntu 的包管理器:

```
sudo apt install git
```

**如果需要，**使用该命令强制安装:

```
sudo apt-get -f install
```

## 原子

要下载并安装最新版本的 Atom，请点击:

```
wget -O atom-amd64.deb [https://atom.io/download/deb](https://atom.io/download/deb)
sudo dpkg -i atom-amd64.deb
```

Atom 附带了一个包管理器(APM ),我们将使用它来安装 linder-solidity:

```
apm install linter
apm install linter-solidity
```

## 松露

为了安装 Truffle，我们将使用 Node 自带的包管理器(NPM ):

```
npm install -g truffle
```

## Geth

首先，下载并解压 geth:

```
cd ~/Downloads
wget [https://gethstore.blob.core.windows.net/builds/geth-alltools-linux-amd64-1.8.3-329ac18e.tar.gz](https://gethstore.blob.core.windows.net/builds/geth-alltools-linux-amd64-1.8.3-329ac18e.tar.gz)
tar -xzf geth-alltools-linux-amd64-1.8.3-329ac18e.tar.gz
```

将二进制文件移动到本地二进制文件夹:

```
mkdir ~/.local/bin
mv geth-alltools-linux-amd64-1.8.3-329ac18e ~/.local/bin/geth
```

并将选择的文件夹添加到系统路径，这样您就可以从任何目录执行它们:

```
echo 'PATH="$HOME/.local/bin/geth:$PATH"' >> ~/.profile
```

重新启动您的终端以重新加载描述文件。如果你在虚拟机上运行，你可能还需要重启它。

要确认 geth 安装正确，请尝试:

```
geth version
```

您应该会看到一些关于当前安装的信息。

## 加纳切

UI 版本可以以 AppImage 格式下载，不需要安装。下载并配置:

```
wget https://github.com/trufflesuite/ganache/releases/download/v1.1.0/ganache-1.1.0-x86_64.AppImage
chmod a+x ganache-1.1.0-x86_64.AppImage
mv ganache-1.1.0-x86_64.AppImage ~/.local/bin/ganache.AppImage
```

要运行，请键入:

```
ganache.AppImage
```

## 以太坊钱包

如果你没有任何解压 ZIP 文件的工具，首先安装 unzip:

```
sudo apt-get install unzip
```

要下载和配置以太坊钱包，请输入:

```
wget https://github.com/ethereum/mist/releases/download/v0.10.0/Ethereum-Wallet-linux64-0-10-0.zip
unzip Ethereum-Wallet-linux64-0-10-0.zip -d ~/.local/bin/ethereumwallet
echo 'PATH="$HOME/.local/bin/ethereumwallet:$PATH"' >> ~/.profile
```

重启您的终端并输入:

```
ethereumwallet
```

# 配置 macOS

## 自制 e 节点

[](https://brew.sh/)**是 macOS 缺少的软件包管理器，我们将在这里使用。我们还将使用[和**节点。JS**](https://nodejs.org/en/) 用于安装一些需要的工具。**

**首先，安装自制啤酒并添加木桶:**

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap caskroom/cask
```

**然后安装节点。我推荐使用 NVM，它允许我们在同一台机器上有许多不同的版本:**

```
touch ~/.bash_profile
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
```

**关闭并重新打开终端和安装节点:**

```
nvm install 8.11.1
```

**您可以使用以下命令检查它是否工作:**

```
node --version
```

## **松露**

**为了安装 Truffle，我们将使用 Node 自带的包管理器(NPM ):**

```
npm install -g truffle
```

**我们将使用自制软件安装所有其他工具。**

## **原子**

```
brew cask install atom
```

## **Geth**

```
brew tap ethereum/ethereum
brew install ethereum
```

## **加纳切**

```
brew cask install ganache
```

## **以太坊钱包**

```
brew cask install ethereum-wallet
```

# **预配置虚拟机映像**

**如果您想跳过配置，直接进入开发阶段，请下载我们在课程中配置使用的映像。唯一的要求是安装 Oracle VirtualBox。**

**该图像有以下版本:**

*   **Ubuntu 64 位版本 16.04.4**
*   **NVM 0.33.8 +节点 JS 8.11.1**
*   **块菌**
*   **获取 1.8.3**
*   **加纳切 1.1.0**
*   **以太坊钱包**

**你可以在这里下载[。密码是区块链。](https://www.dropbox.com/s/ha3chvm8snip85w/Ubuntu%2016.04.4.ova?dl=0)**

**在接下来的文章中，我将解释如何创建和发布你的第一个合同，以及如何使用 web3.js 与它交互。再见！**