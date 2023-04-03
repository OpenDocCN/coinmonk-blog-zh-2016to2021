# 在 Mainnet 上安装和运行 Ethereum 2 Prysm 验证程序

> 原文：<https://medium.com/coinmonks/installing-and-running-an-ethereum-2-prysm-validator-on-the-testnet-c004dd7eece8?source=collection_archive---------0----------------------->

![](img/f99b13e7e76eb73628d9ed4c195beec4.png)

连锁开始:2020 年 12 月 1 日

上次测试和更新时间:2020 年 12 月 9 日

源代码哈希:ade 3 B2 F2 dffc 242341 DFC 0793984 b 431 a 889 e5b 2

Ubuntu: 20.04

技术技能:对 Linux 的基本理解

![](img/16a69fefdcff5e8f425c9722d4bff092.png)

本文档的目的是为在 Prysm Ethereum 2 Mainnet 上基本安装和运行验证程序提供一个简单的指南。这是启动和运行一个节点的最基本步骤，如果您需要更多的细节，请参考 Prysm 文档。更多详细说明可在 [Prysm 网站](https://docs.prylabs.network/docs/getting-started/)上找到。运行验证器需要考虑更多的复杂性，比如设置防火墙、滚动日志等，但这是运行 Prysm 验证器的最低要求。

这些是在 Ubuntu 20.04 上安装 Mainnet 节点的步骤，并且可能在不同的代码版本上工作，但是我们只在上面的源代码散列上测试了它。[证明人](https://www.attestant.io/)正在不断建立节点和更新本文档，如果您有任何问题，请让我们知道。这些说明用于在 2020 年 12 月 1 日启动的 Mainnet 信标链上的上述指定日期构建节点。下面的说明还向您展示了如何获取您下载的软件的当前版本的源代码散列。

认证者在配备 64GB 和 1TB 固态硬盘的第八代 1.80 GHz Kaby Lake 英特尔酷睿 i5–8250 u/i7–8550 u 处理器上测试了安装。

**注意:**每个新的命令行都以$开头，并且每一行都需要单独运行并返回。$是命令提示符，不应复制。

# 安装以太坊 1 Geth 节点

以太坊 2 要求你在以太坊 1 主网上与存款合同进行交互。所以下一步是安装并运行一个 geth Ethereum 1 节点。警告:这将需要几天的时间来同步，目前占用 330GB 的磁盘空间。

## 安装 Geth 和屏幕

```
$ sudo add-apt-repository -y ppa:ethereum/ethereum
$ sudo apt install ethereum screen -y
```

## 运行以太网 1 维护网

使用上面安装的工具“ [screen](https://linuxize.com/post/how-to-use-linux-screen/) ”运行 geth，独立于终端进程。

```
$ sudo mkdir -p /var/log/geth && sudo chown $USER /var/log/geth
$ screen -S Eth1Mainnet
$ geth --http &>> /var/log/geth/etheruem1Mainnet.log
$ Ctrl+a d
```

Etheruem 1 需要运行并完全同步，以便 Prysm 信标节点和验证器可以确定 ETH1 存款的时间。

要查看 sycn 的进度，您可以跟踪日志:

```
$ tail -f /var/log/geth/etheruem1Mainnet.log
```

# 安装 Prysm 客户端

代码每天都在变化，所以要做好准备，定期下载代码。

```
$ sudo apt-get install cmake git -y
$ git clone [https://github.com/prysmaticlabs/prysm.git](https://github.com/prysmaticlabs/prysm.git)
$ cd prysm
```

使用本文档顶部的散列对指令进行了测试。要获得您下载的当前版本的源代码散列，请运行以下命令:

```
$ git rev-parse HEAD
```

# 运行信标链

以下命令将启动信标链；将链数据写入 beaconChain 目录. eth2，并将日志输出到主目录中的日志文件。

```
$ sudo mkdir -p /var/log/prysm && sudo chown $USER /var/log/prysm
% screen -S PrysmBeaconChain
$ $HOME/prysm.sh beacon-chain --http-web3provider=[http://127.0.0.1:8545/](http://127.0.0.1:8545/) --log-file=/var/log/prysm/prysmBeaconChain.log 
$ Ctrl+a d
```

要获得软件的最新版本，您只需重启信标链和验证器，它将自动下载软件，编译并运行。要重新进入屏幕程序，您只需重新连接到该屏幕。如果你想终止进程，这很有用。

```
$ screen -r PrysmBeaconChain
```

如果您想实时查看日志，那么您可以将日志文件放在/var/log/prysm/中。

```
$ tail -f /var/log/prysm/prysmBeaconChain.log
```

# 创建验证和撤回密钥

如果你想在 Mainnet 上真正做到这一点，那么通过以太坊基金会的 [launchpad](https://launchpad.ethereum.org/summary) 创建你的密钥。我们建议在存放 Mainnet 之前，先在 Pyrmont [launchpad](https://pyrmont.launchpad.ethereum.org/) 上浏览一遍整个过程，让自己熟悉整个过程。如果您使用 launchpad，您可以跳过整个密钥生成过程。我们也不包括为您的验证器和提取密钥创建存款数据并将其发送到以太坊 1，因为 Launchpad 涵盖了所有这些步骤。

在本文档中，我们将向您展示如何使用 Prysm 创建帐户，但在 Attestant 内部，我们使用 ethdo，有关替代钱包的说明可在[这里](https://steve-berryman.medium.com/creating-ethereum-2-withdrawal-keys-using-ethdo-6e41b14ddd7b)找到。

随机生成复杂的密码短语是一种很好的安全做法。使用密码生成器，例如 pwgen，创建一个 24+大小写混合的字母数字密码短语(pwgen -B 24 -c 1)。将此密码保存在安全的地方，如密码管理器。运行以下命令来安装、运行并将结果存储在名为 password.txt 的文件中:

```
$ sudo apt install pwgen
$ pwgen -B 24 -c 1 -y -n > password.txt
```

## 创建一个包含多个帐户的钱包

为了参与赌注，你需要创建一个以太坊 2 验证器和提取密钥。关于这些键的作用的更多信息，你可以看到我们的[博客](https://www.attestant.io/posts/exploring-staking-keys/)和一个关于撤回键的具体博客[你可以在这里看到](https://www.attestant.io/posts/protecting-withdrawal-keys/)。

在下一步中，我们将创建一个钱包，其中包含从一组种子中生成的多个帐户。在这种情况下，我们将*钱包*定义为一个以太坊 2 密钥的集合，并将*账户*定义为一个以太坊 2 密钥。

一个验证器可以验证多个键，所以您可以继续创建多个键。运行以下命令，并指定要创建的帐户数量:

```
$ $HOME/prysm/prysm.sh validator wallet create --wallet-dir=$HOME/myTestWallet --keymanager-kind=derived --wallet-password-file=password.txt --skip-mnemonic-25th-word-check=false
```

要列出您刚刚创建的帐户:

```
$ $HOME/prysm/prysm.sh validator accounts list --wallet-dir=myTestWallet --wallet-password-file=password.txt
```

## 恢复钱包

在您使用钱包之前，您应该确保可以从您的种子词中恢复它。因此，删除钱包，并通过运行以下命令检查它是否可以恢复，该命令将提示您输入种子短语:

```
$ rm -rf $HOME/myTestWallet
$ $HOME/prysm/prysm.sh validator wallet recover --wallet-password-file=password.txt --wallet-dir=$HOME/myTestWallet --skip-mnemonic-25th-word-check=false
```

# **运行验证器**

现在我们已经运行了信标链，创建了验证器和取款密钥，并将存款数据发送到 Eth1 契约，现在我们准备运行验证器流程。运行下面的命令，验证程序会一直等待，直到它看到 Ethereum 1 Mainnet 上为您的密钥引发了存款事件。

```
$ cd prysm
$ screen -S ValidatorTestChain
$ $HOME/prysm/prysm.sh validator --wallet-dir=$HOME/myTestWallet  --wallet-password-file=password.txt
$ Ctrl+a d
```

如果您想实时查看日志，那么您可以将日志文件放在您的主目录中。

```
$ tail -f $HOME/validator.log
```

下面是等待验证密钥生效的验证器日志:

```
validator-v1.0.4-linux-amd64: OK
gpg: Signature made Mon 07 Dec 2020 17:46:35 GMT
gpg:                using RSA key 0AE0051D647BA3C1A917AF4072E33E4DF1A5036E
gpg: Good signature from "Preston Van Loon <[preston@prysmaticlabs.com](mailto:preston@prysmaticlabs.com)>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 0AE0 051D 647B A3C1 A917  AF40 72E3 3E4D F1A5 036E
Verified /home/prysm/dist/validator-v1.0.4-linux-amd64 has been signed by Prysmatic Labs.
Starting Prysm validator --wallet-dir=/home/myTestWallet --wallet-password-file=password.txt
[2020-12-09 12:07:10]  WARN flags: Running on ETH2 Mainnet
[2020-12-09 12:07:10]  INFO node: Opened validator wallet keymanager-kind=derived wallet=/home/myTestWallet/derived
[2020-12-09 12:07:10]  INFO node: Checking DB databasePath=/home/myTestWallet/derived
[2020-12-09 12:07:10]  INFO node: Starting validator node version=Prysm/v1.0.4/b4437e6cecd78257d2699f42073a90a940baf716\. Built at: 2020-12-07 16:42:42+00:00
[2020-12-09 12:07:10]  WARN validator: You are using an insecure gRPC connection. If you are running your beacon node and validator on the same machines, you can ignore this message. If you want to know how to enable secure connections, see: [https://docs.prylabs.network/docs/prysm-usage/secure-grpc](https://docs.prylabs.network/docs/prysm-usage/secure-grpc)
[2020-12-09 12:07:10]  INFO validator: Waiting for beacon chain start log from the ETH 1.0 deposit contract
[2020-12-09 12:07:10]  INFO validator: Validating for public key publicKey=0x820911a11f28
[2020-12-09 12:07:10]  INFO validator: Validating for public key publicKey=0xa8382c7fb4be
[2020-12-09 12:07:10]  INFO validator: Validating for public key publicKey=0xa2915cc13106
[2020-12-09 12:07:10]  INFO validator: Validating for public key publicKey=0x81003ebb24fb
[2020-12-09 12:07:10]  INFO validator: Validating for public key publicKey=0x870e7ea5e3a8
```

# 有用的链接

[创建以太坊 2 撤回密钥](https://steve-berryman.medium.com/creating-ethereum-2-withdrawal-keys-using-ethdo-6e41b14ddd7b)

见证人[以太坊打桩](https://www.attestant.io/posts/)岗位

[以太坊 2.0 锁定](https://www.attestant.io/posts/exploring-staking-keys/)键

[以太坊 2.0 提现键](https://www.attestant.io/posts/protecting-withdrawal-keys/)

# 接触

如果您想了解更多关于证明人的信息，您可以通过电子邮件[info @证明人. io](mailto:info@attestant.io) 或电报[https://t.me/attestant](https://t.me/attestant)与我们联系

## 另外，阅读

*   [最佳加密交易机器人](/coinmonks/whats-the-best-crypto-trading-bot-in-2020-top-8-bitcoin-trading-bot-c16adeb13317)
*   [Deribit 审查](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和 Testnet
*   [FTX 密码交易所评论](/coinmonks/ftx-crypto-exchange-review-53664ac1198f)
*   [比特交换评论](/coinmonks/bybit-exchange-review-dbd570019b71)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   [密码本交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   最佳[加密贷款平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [莱杰纳米 S vs 特雷佐 1 vs 特雷佐 T vs 莱杰纳米 X](https://blog.coincodecap.com/ledger-nano-s-vs-trezor-one-ledger-nano-x-trezor-t)
*   [block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)vs Hodlnaut
*   Bitsgap 评论——一个轻松赚钱的加密交易机器人
*   为专业人士设计的加密交易机器人
*   [PrimeXBT 审查](/coinmonks/primexbt-review-88e0815be858) |杠杆交易、费用和交易
*   [在线评论](/coinmonks/haasonline-review-d8d1a3400419)享受九折优惠
*   [埃利帕尔泰坦评论](/coinmonks/ellipal-titan-review-85e9071dd029)
*   [赛克斯·斯通评论](https://blog.coincodecap.com/secux-stone-hardware-wallet-review)
*   [BlockFi 评论](/coinmonks/blockfi-review-53096053c097) |赚取高达 8.6%的加密利息
*   开发人员的最佳加密 API
*   [最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱
*   顶级[比特币节点](https://blog.coincodecap.com/bitcoin-node-solutions)提供商
*   最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)
*   了解比特币最好的[书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)

> [直接在您的收件箱中获得最佳软件交易](/coinmonks/newsletters/coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://medium.com/coinmonks/newsletters/coinmonks)