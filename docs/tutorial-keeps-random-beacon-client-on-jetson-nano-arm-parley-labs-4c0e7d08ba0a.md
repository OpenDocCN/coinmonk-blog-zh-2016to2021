# 教程:Jetson Nano (ARM)上 Keep 的随机信标客户端— Parley Labs

> 原文：<https://medium.com/coinmonks/tutorial-keeps-random-beacon-client-on-jetson-nano-arm-parley-labs-4c0e7d08ba0a?source=collection_archive---------2----------------------->

![](img/8be0d8618dd29e5cbf39d4f314ec0648.png)

如果你对 keep.network 不熟悉，你可以读一些这样的文章来充实自己，但是如果你偶然发现了这个页面，你可能已经知道了，所以让我们开始吧:

资源:
[介绍 Keep](https://blog.keep.network/introducing-keep-2a186ebffd44)
[如何获得 KEEP — Stake ETH](https://blog.keep.network/how-to-get-keep-stake-eth-42252ee11863)

本指南将指导您设置您的 NVIDIA Jetson Nano，在以太坊的 Reposten testnet 上运行 [Keep Random Beacon](https://github.com/keep-network/keep-core/blob/master/docs/run-random-beacon.adoc) ，并在本地运行 GETH light 客户端。我们将在这里从源代码构建，因为我在从 Docker 构建时遇到了麻烦(我将在接下来探讨这一点)。一些说明来自于[https://www . idea . so/Keep-Testnet-User-Guide-3d 67 D4 a 185 AC 4 e 11 be 86 b 51 a 02256 fab](https://www.notion.so/Keep-Testnet-User-Guide-3d67d4a185ac4e11be86b51a02256fab)和[https://medium . com/@ novysf/run-a-Keep-network-Testnet-node-37096946 af 35](/@novysf/run-a-keep-network-testnet-node-37096946af35)，它们帮助我开始并弄清楚如何在我的设备上设置东西。谢谢你给我们指路！

*这应该适用于任何 ARM 设备，如 Raspberry Pi，但我还没有测试过。

1.  ARM 设备上运行 Ubuntu 18.04。
2.  以 root 用户身份运行
3.  安装完毕
4.  Geth +开发人员工具(见下文)
5.  可靠性编译器(见下文)
6.  保持核心(见下文)

# 准备系统

1.  将您的 Jetson Nano 安装在新的驱动器上:[https://developer . NVIDIA . com/embedded/learn/get-started-Jetson-Nano-dev kit](https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit)。这样你就可以安装 Ubuntu 18.04 了。
2.  [启用 root 登录](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/v2v_guide/preparation_before_the_p2v_migration-enable_root_login_over_ssh):以 Root 身份编辑`/etc/ssh/sshd_config`中的 sshd_config 文件:

```
nano /etc/ssh/sshd_config
```

在文件的 Authentication 部分添加一行，说明`*PermitRootLogin yes*`。该行可能已经存在，可以用“#”注释掉。在这种情况下，请删除“#”。

```
# Authentication: #LoginGraceTime 2m PermitRootLogin yes #StrictModes yes #MaxAuthTries 6 #MaxSessions 10
```

保存更新后的`/etc/ssh/sshd_config`文件。重新启动 SSH 服务器:

```
service sshd restart
```

现在，您可以通过 SSH 以 root 身份连接到转换服务器。

# 设置 GO

首先安装 GETH，我们将安装 GO，其他版本也需要它。我们将为我们的(arm V8)【https://golang.org/dl/[和](https://golang.org/dl/)主板发布最新版本(在撰写本文时为 1.14 版)。*树莓 Pi 3 也运行 ARMv8。

```
tar -C /usr/local -xzf go1.14.2.linux-arm64.tar.gzexport PATH=$PATH:/usr/local/go/bin
```

# 设置 GETH

2020 年 4 月 28 日更新:

去 https://geth.ethereum.org/downloads/[下载 Linux Arm64 稳定版的 Geth &工具档案。如果您想从源代码开始构建，可以继续下面的内容。](https://geth.ethereum.org/downloads/)

我们将从源代码构建 GETH，因为我们需要一些开发人员工具来构建这个项目。

安装 Go 后，您可以通过以下方式将项目下载到您的`GOPATH`工作区:

```
go get -d github.com/ethereum/go-ethereum
```

将您的目录更改为 go-ethereum 文件夹:

```
cd /root/go/src/github.com/ethereum/go-ethereum/
```

构建整个项目并安装 geth 以及所有开发人员工具:

# 建造坚固性

构建可靠性编译器(因为 solc 是在 ARM 上运行 go 构建所必需的功能)

[https://solidity . readthedocs . io/en/v 0 . 5 . 3/installing-solidity . html # clone-the-repository](https://solidity.readthedocs.io/en/v0.5.3/installing-solidity.html#clone-the-repository)

要克隆源代码，请执行以下命令:

```
git clone --recursive <https://github.com/ethereum/solidity.git> cd solidity
```

确保您签出了 solidity 版本 0 . 5 . 17(keep-core repo 所要求的)

```
git checkout tags/v0.5.17
```

以下脚本在 macOS、Windows 和许多 Linux 发行版上安装所有必需的外部依赖项。

```
./scripts/install_deps.sh
```

构建并安装 solc

```
#note: this will install binaries solc and soltest at usr/local/bin ./scripts/build.sh
```

# 正在创建 Ropsten 密钥库

在这里，我们将为新的钱包操作员地址创建一个密钥库。为此，您需要打开两个终端:

```
geth --ropsten --nousb --syncmode "light" -rpc --rpcapi="eth,net,web3,personal,web3" -wsgeth --ropsten attachpersonal.newAccount()
```

*   现在我们已经创建了 geth 控制台的操作员帐户`exit`。
*   记下该帐户的密钥库文件的名称:

```
ls ~/.ethereum/testnet/keystore
```

将您的密钥库文件复制到/root/keepclient/

```
cd /root mkdir keepclient cp /root/.ethereum/testnet/keystore/<keystore file name> /root/keepclient/keystore
```

# 让 KEEP 和 ETH 在 testnet 上运行

*   从 Ropsten 水龙头滴一些 testnet ETH 到你的运营商账户:[https://faucet.ropsten.be/](https://faucet.ropsten.be/)。运营商账户需要一些 ETH 来支付运营用气。
*   使用您的所有者帐户地址，通过访问以下 URL 请求 Keep 令牌授权:[https://us-central 1-Keep-test-F3 E0 . cloud functions . net/Keep-水龙头-ropsten？帐户=](https://us-central1-keep-test-f3e0.cloudfunctions.net/keep-faucet-ropsten?account=) 操作员 _ 帐户 _ 地址
*   打开 [Keep Dashboard](https://dashboard.test.keep.network/tokens) ，使用 Metmask 连接您的所有者帐户，并将您的令牌授权委托给操作员帐户。将您的所有者帐户用于受益人和授权人地址。
*   在 Keep Dashboard 的[授权页面](https://dashboard.test.keep.network/authorizer)中，授权您的`config,toml`中指定的`KeepRandomBeaconOperator`地址。

# 为保留核心创建配置

创建一个`config.toml`文件，用前面步骤中获得的值替换您的`OPERATOR_ACCOUNT_ADDRESS`和`KEYSTORE_FILE_NAME`。你还需要用你系统的 IP 地址替换`VPS_IP_ADDRESS`，我把它注释掉了，因为我没有公布我的模块，因为它在防火墙后面。

请注意，发布时契约地址和引导对等项是正确的，但是您应该在[https://docs . keep . network/run-random-beacon . html # _ testnet](https://docs.keep.network/run-random-beacon.html#_testnet)中检查最新值。

```
# Ethereum host connection info. 
[ethereum]   
URL = "ws://127.0.0.1:8546"   
URLRPC = "http://172.0.0.1:8545" # Keep operator Ethereum account. 
[ethereum.account]   
Address = "OPERATOR_ACCOUNT_ADDRESS"   
KeyFile = "/root/keepclient/keystore" # Keep contract addresses configuration. 
[ethereum.ContractAddresses]   
# Hex-encoded address of KeepRandomBeaconOperator contract   
KeepRandomBeaconOperator = "0xe1f5c786e5958935878eacb844bbe74767e9c3e9"   
# Hex-encoded address of TokenStaking contract   
TokenStaking = "0x09b3B8370C2683c9eFC5be5A58643AdaFC412AaC"   
# Hex-encoded address of KeepRandomBeaconService contract. Only needed   
# in cases where the client's utility functions will be used (e.g., the   
# relay subcommand).   
KeepRandomBeaconService = "0x3dE1c24a19d9bd89b4d4Ea4b23645481480DB0be"  
# Keep network configuration. 
[LibP2P]   
Peers = ["/dns4/bootstrap-1.test.keep.network/tcp/3919/ipfs/16Uiu2HAm3eJtyFKAttzJ85NLMromHuRg4yyum3CREMf6CHBBV6KY", "/dns4/bootstrap-2.test.keep.network/tcp/3919/ipfs/16Uiu2HAmNNuCp45z5bgB8KiTHv1vHTNAVbBgxxtTFGAndageo9Dp", "/dns4/bootstrap-3.test.keep.network/tcp/3919/ipfs/16Uiu2HAm8KJX32kr3eYUhDuzwTucSfAfspnjnXNf9veVhB12t6Vf", "/dns4/bootstrap-4.test.keep.network/tcp/3919/ipfs/16Uiu2HAkxRTeySEWZfW9C83GPFpQUXvrygmZryCN6DL4piZrbAv4"]   
Port  = 3919   
# Override the node's default addresses announced in the network   
# AnnouncedAddresses = ["/ip4/VPS_IP_ADDRESS/tcp/3919"]  
# Storage is encrypted [Storage]   DataDir = "/keepclient/data"
```

# 构建保留核心

安装协议缓冲区:[https://github . com/protocol buffers/proto buf/blob/master/src/readme . MD](https://github.com/protocolbuffers/protobuf/blob/master/src/README.md)

安装依赖项:

```
$ sudo apt-get install autoconf automake libtool curl make g++ unzip
```

获取协议缓冲区

```
cd /root git clone <https://github.com/protocolbuffers/protobuf.git> cd protobuf git submodule update --init --recursive ./autogen.sh
```

要构建并安装 C++协议缓冲运行时和协议缓冲编译器(Protocol ),请执行以下操作:

```
./configure make make check make install ldconfig # refresh shared library cache.
```

安装协议-gen-gogoslick

```
go get -u [github.com/gogo/protobuf/protoc-gen-gogoslick](<http://github.com/gogo/protobuf/protoc-gen-gogoslick>)
```

返回 keep-core 目录并构建:

```
cd /root git clone <https://github.com/keep-network/keep-core.git> git checkout tags/v0.14.1-rc # gets the current release build cd keep-core
```

现在生成并构建 keep-core:

```
go generate ./... go build -a -o keep-core .
```

运行 [run.sh](http://run.sh) 脚本并遵循说明:

```
# from /root/keep-client ./scripts/run.sh
```

*原载于 2020 年 4 月 27 日 https://parleylabs.com*[](https://parleylabs.com/2020/04/26/tutorial-keeps-random-beacon-client-on-jetson-nano-arm/)**。**

*![](img/e9dbce386c4f90837b5db529a4c87766.png)*

> *[直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)*

*[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)*