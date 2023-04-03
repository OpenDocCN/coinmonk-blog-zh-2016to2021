# 如何设置 Filecoin Lotus node 并远程连接到它

> 原文：<https://medium.com/coinmonks/how-to-set-up-a-filecoin-lotus-node-and-connect-remotely-to-it-208c2f810060?source=collection_archive---------1----------------------->

![](img/1ea2b1724a6af42313457df1c17de583.png)

在包装的 Filecoin (WFIL)，我们正在以太坊上开发第一个 Filecoin 包装的令牌。为了实现我们的目标，我们需要能够详细探索和分析 Filecoin 的区块链。

习惯于使用以太坊，我们可以很快想到几个工具来做到这一点，但目前，Fielecoin 还处于非常早期的阶段，这些工具还不可用。

所以我们决定建立我们自己的 Lotus node，并配置它允许我们从后端 API 调用它的 JSON RPC 方法。我们遵循的主要说明位于这里: [Filecoin Lotus 安装文档](https://docs.filecoin.io/get-started/lotus/installation/)但是由于我们需要修改一些步骤，我们认为值得分享它，并帮助任何有相同问题的人。

## 1.启动虚拟服务器并通过 SSH 连接

在我们的例子中，我们使用 AWS 作为云提供商，所以我们设置了一个 m5ad.2xlarge 类型的 EC2。这里对**选择 Ubuntu Server 20.04 LTS** 很重要，因为安装 Lotus 和 Go 所需的所有工具和库在亚马逊 Linux 系统上都不可用。

要通过 SSH 连接到 AWS 实例，请确保已经将安全组配置为允许通过端口 22 进行远程访问。之后，您应该使用在启动实例时生成的 PEM 证书，但是首先，让我们设置能够使用它的适当权限:

```
chmod 400 <path-to-pem-file>
```

现在我们可以连接到 EC2 实例:

```
ssh -i <path-to-pem-file> ubuntu@<ip-of-your-instance>
```

## 2.安装依赖项，运行并生锈

这里没什么可做的，我们只需遵循 Filecoin 网站上的指南即可:

依赖关系:

```
sudo apt install mesa-opencl-icd ocl-icd-opencl-dev gcc git bzr jq pkg-config curl clang build-essential libhwloc-dev -y && sudo apt upgrade -y
```

Rustp:

```
curl --proto '=https' --tlsv1.2 -sSf [https://sh.rustup.rs](https://sh.rustup.rs) | sh
```

去吧:

```
wget -c [https://dl.google.com/go/go1.14.7.linux-amd64.tar.gz](https://dl.google.com/go/go1.14.7.linux-amd64.tar.gz) -O - | sudo tar -xz -C /usr/local
```

## 3.配置环境变量

文档中没有很好地解释这一步，如果你不熟悉处理这类软件，这可能是一个棘手的部分。

首先，我们应该向环境配置中添加变量。让我们打开 bash 概要文件:

```
nano ~/.bashrc
```

通过在文件末尾添加以下行，为 go 和 rust 添加变量:

```
export PATH=$PATH:/usr/local/go/bin
export PATH=$PATH:/home/ubuntu/.cargo/bin
```

获取文件或重启终端:

```
source ~/.bashrc
```

## 4.安装并启动 Lotus 守护程序

和以前一样，我们只是执行给定的命令。

克隆存储库:

```
git clone [https://github.com/filecoin-project/lotus.git](https://github.com/filecoin-project/lotus.git)
```

检查你选择的分支(mainnet 或任何 testnet)。我们选择校准，因为它是最能模拟 mainnet 的测试网络:

```
git checkout ntwk-calibration
```

构建和安装

```
make clean all
sudo make install
```

检查版本:

```
lotus --version #should output something like lotus version 1.1.0+git.b039f44a
```

## 5.允许远程连接

打开 lotus 配置`~/.lotus/config.toml`。您将看到要配置的不同部分，以及您可以设置的值的示例。我们对 ListenAddress 和 RemoteListenAddress 值的 API 部分感兴趣。删除他们的注释，并为每个注释设置以下值:

```
ListenAddress = "/ip4/0.0.0.0/tcp/4001/http"
RemoteListenAddress = "/ip4/xxx.xxx.xxx.xxx/tcp/4001/http"
```

确保用您的实例**公共 IP** 替换`xxx.xxx.xxx.xxx`，并设置最适合您的端口(还要注意，这个端口也应该在您的 EC2 实例的安全组上打开)。

启动守护程序:

```
lotus daemon
```

创建一个令牌来远程访问它(例如作为管理员)

```
lotus auth create-token --perm admin
```

使用令牌从不同的计算机调用您的实例，并测试我们所做的一切:

```
curl -X POST      -H "Content-Type: application/json"      -H "Authorization: Bearer <the-token-you-just-generated>"      --data '{ "jsonrpc": "2.0", "method": "Filecoin.Version", "params": [], "id": 1 }'      '[http://xxx.xxx.xxx.xxx:4001/rpc/v0'](http://54.216.161.54:5000/rpc/v0')
```

输出应该是如下所示的 JSON 响应:

```
{"jsonrpc":"2.0","result":{"Version":"1.1.0+git.b039f44a","APIVersion":4352,"BlockDelay":30},"id":1}
```

## 6.[奖励]设置 pm2 总是启动守护进程

如果实例重新启动或守护进程停止，我们应该意识到这一点，连接到实例，并重新启动守护进程。在这些情况下，拥有一个像 pm2 这样的库可以省去很多麻烦，并在需要时重启守护进程。

要配置它，您应该安装 NodeJS、NPM 和 pm2:

```
sudo apt install nodejs
sudo apt install npm
npm install pm2@latest -g
```

然后，我们应该创建一行 shell 脚本来启动守护程序，配置 pm2，并使它能够启动它:

```
echo lotus daemon >> start-lotus-daemon.sh
sudo env PATH=$PATH:/usr/bin /usr/local/lib/node_modules/pm2/bin/pm2 startup systemd -u ubuntu --hp /home/ubuntu/start-lotus-daemon.sh
```

从 pm2 启动守护程序

```
pm2 start start-lotus-daemon.sh
```

并随时监控它:

```
pm2 monit
```

## 结论

建立自己的 Lotus node 似乎是一项艰巨的工作，但是完全控制从 Filecoin 的区块链获得的数据是非常值得的。

请随时查看 [WFIL dapp](https://www.wfil.network/#/) 并让我们知道您的任何想法。我们真的希望与社区携手在以太坊上构建第一个 Filecoin 包装的令牌。

特别感谢[斯瓦鲁普](https://twitter.com/swarooph)和[穆斯塔法](https://www.linkedin.com/in/mostafa-farghaly)在这个过程中给予的所有帮助。

## 另外，阅读

*   最好的[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)