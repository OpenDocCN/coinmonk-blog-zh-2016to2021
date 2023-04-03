# Python 中 Neo 智能合约的本地开发

> 原文：<https://medium.com/coinmonks/local-development-of-neo-smart-contracts-in-python-1f4a879ca3b?source=collection_archive---------1----------------------->

**注:**本文最初是为[锡安城竞赛#3](/proof-of-working/coz-competition-3-award-winners-a5552efef32e) 创作的，是一篇亚军作品。最初的帖子在 [Github](https://github.com/nevantan/neo-tutorial) 上，但是我已经把它转移到了 [Gitlab](https://gitlab.com/nevantan/neo-tutorial/) 。

在本教程中，我们将使用 [neo-local](https://github.com/CityOfZion/neo-local) 项目为 neo 智能合约的本地开发和测试设置一个私有链。使用私有链给了我们对环境的完全控制，允许我们在不处理外部测试网的情况下独立工作。

为了跟进，您需要访问一个类似 Unix 的终端和某种文本编辑器。我将在虚拟机中工作，使用 nano 进行文本编辑:

Ubuntu 18.04(最小安装)
4GB 内存
50GB 磁盘

请注意，您可能需要至少 20GB 的磁盘空间来存储您的私有链。

# Docker、Docker Compose 和新本地

新本地项目需要 Docker 来运行，所以首要任务是安装 Docker。Docker 是一个容器引擎，可以运行预先配置的设置，这正是 neo-local 使用它的目的。我们将使用 Docker 社区版(Docker CE)。

## 安装 Docker

您可以在 [Docker 文档](https://docs.docker.com/install/)中找到所选操作系统的详细安装说明。以下是更常见操作系统选项的直接链接:

*   [窗户](https://docs.docker.com/docker-for-windows/install/)
*   [MacOS](https://docs.docker.com/docker-for-mac/install/)
*   [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)(Linux 的[安装后步骤——也遵循这些)](https://docs.docker.com/install/linux/linux-postinstall/)

## 安装 Docker 撰写

我们还需要 Docker 撰写。对于 Windows 和 MacOS，它应该已经包含在您在上一步中安装的软件包中。Linux 用户需要遵循文档的[撰写部分概述的步骤。请务必按照指南中的指示检查](https://docs.docker.com/compose/install/) [GitHub 发布页面](https://github.com/docker/compose/releases)中的最新版本号——不要不经检查就复制/粘贴命令。

## 测试码头工人

作为一个快速测试，您现在应该能够运行以下命令(以' $ '开头的行)并看到相应的输出:

```
$ docker --version
Docker version 18.06.0-ce, build 0ffa825
$ docker-compose --version
docker-compose version 1.22.0, build f46880fe
```

您的版本号可能与我的版本号不完全匹配，只要您运行的是最新版本，这没关系。

如果安装后的步骤有效的话，Linux 用户也应该能够在没有`sudo`的情况下运行“hello-world”docker 容器。请注意，如果出现某种“权限被拒绝”的错误，您可能需要重新启动计算机:

```
$ docker run hello-world
```

## 设置新本地

最后，我们需要通过克隆存储库和挂载预配置的 wallet 文件来设置 neo-local，这样我们就可以在我们的私有网络上使用 GAS。

在您的终端中，导航到您想要存储 Neo 相关作品的目录，然后克隆存储库。对于那些不熟悉 git 的人来说，这会创建一个名为“neo-local”的目录，里面有我们需要的文件。导航到新本地目录。

```
$ git clone https://github.com/CityOfZion/neo-local.git
$ cd neo-local
```

这里的大部分文件都与新本地项目本身有关，我们不会对它们进行任何修改。我们稍后将只在设置过程中处理`wallets/`目录。一旦我们启动并运行，我们的大部分工作都将在`smart-contracts/`目录中。

我们将使用的钱包文件有点难以定位。你可以在 [neo-privatenet Docker hub 页面](https://hub.docker.com/r/cityofzion/neo-privatenet/)上找到它，或者直接从[这个直接链接](https://s3.amazonaws.com/neo-experiments/neo-privnet.wallet)获取它。将下载的 wallet 文件放在新本地存储库的`wallets/`目录中。作为参考，这个钱包的密码是`coz`。

## 启动新本地堆栈

在不同的操作系统之间，启动堆栈略有不同。这两组命令将让您进入一个新的 python 命令行界面(CLI)。

**Windows(**[**wiki**](https://github.com/CityOfZion/neo-local/wiki/Usage-(Windows))**)**

```
$ docker-compose up -d --build --remove-orphans
$ docker exec -it neo-python np-prompt -p -v
```

**MAC OS(**[**wiki**](https://github.com/CityOfZion/neo-local/wiki/Usage-(Mac))**)和 Linux(**[**wiki**](https://github.com/CityOfZion/neo-local/wiki/Usage-(Linux))**)**

如果您还没有安装`make`,请安装:

```
$ sudo apt install make
```

启动堆栈:

```
$ make start
```

## 打开钱包

使用智能合约之前的最后一个设置步骤是打开我们之前复制的钱包。Docker 设置为在根目录下挂载`wallets/`目录，所以我们的钱包驻留在`/wallets/neo-privnet.wallet`。neo-python 中的`help`命令将显示可能的命令列表。我们要找的是`open wallet {path}`，它会提示输入钱包的密码(`coz`)。整个事情应该如下所示:

```
neo> open wallet /wallets/neo-privnet.wallet
[password]> ***
Opened wallet at /wallets/neo-privnet.wallet
neo>
```

# 基本智能合同

由于 neo-local 栈使用 neo-python，我们将用 python 编写一个基本的智能契约。在`smart-contracts/`目录中创建文件`plus_one.py`，并添加以下代码:

```
def Main(num):
  return num + 1;
```

如您所见，契约接受一个输入数字，并返回该数字加 1。与`wallets/`目录非常相似，`smart-contracts/`也挂载在根目录下，因此我们的契约的路径将是`/smart-contracts/plus_one.py`。

Neo 智能合约在 NeoVM (Neo 虚拟机)上运行，在部署之前，必须首先转换为字节码，类似于 Java。neo-python 中的`build {path}`命令为我们完成了这项工作，它在作为输入提供的`.py`文件旁边生成一个`.avm`文件。然而，这个文件本身并没有给我们带来多少好处。我们希望从命令的`build..test`变体开始:

```
neo> build /smart-contracts/plus_one.py test 02 02 False False False 5
```

在我们得到输出之前，让我们分解这个命令。该命令的完整签名是:

```
neo> build {path/to/file.py} test {param_types} {return_type} {needs_storage} {needs_dynamic_invoke} {is_payable} [params]
```

文件的路径是不言自明的。然而，类型是作为一个 [ContractParameterType](http://docs.neo.org/en-us/sc/Parameter.html) 提供的，其中参数和返回类型都表示为一个字节:

文件的路径是不言自明的。然而，类型是作为 [ContractParameterType](http://docs.neo.org/en-us/sc/Parameter.html) 提供的，其中参数和返回类型都表示为单个字节:

在我们的例子中，契约接受一个整数(02)并返回一个整数(02)。接下来的三个参数是在契约上设置的属性，我们现在不用担心这些。最后一个参数是填写实际调用契约时使用的数据。在 neo-python 提示符下运行该命令应该会输出如下内容:

```
neo> build /smart-contracts/plus_one.py test 02 02 False False False 5
[I 180909 22:53:38 BuildNRun:50] Saved output to /smart-contracts/plus_one.avm
[I 180909 22:53:38 Invoke:586] Used 0.021 Gas

-----------------------------------------------------------
Calling /smart-contracts/plus_one.py with arguments ['5']
Test deploy invoke successful
Used total of 19 operations
Result [{'type': 'Integer', 'value': 6}]
Invoke TX gas cost: 0.0001
-------------------------------------------------------------

neo>
```

第一个输出行是确认字节码的构建和保存位置。输出的其余部分描述了协定的设置和结果。我们用值‘5’调用它，调用成功，我们收到了整数‘6’。看起来我们的合同有效！

# 部署智能合同

最后，一旦我们构建并测试了智能合约，我们需要将它部署到网络中。这实际上是一个相当简单的过程，我们只需要使用`import contract`命令。

```
neo> import contract /smart-contracts/plus_one.avm 02 02 False False False
```

系统会提示您填写一些字段，如合同名称和版本、作者姓名和电子邮件等。一旦这些被填写，一些关于合同的元数据将被打印出来，你将被提示输入你的钱包密码。输入此密码将展开合同，并向您收取所需的汽油量。由于这是一个专用网络，您应该可以继续进行部署——测试钱包有足够的气体来工作。

部署完成后，从打印的元数据中获取“散列”并运行一个`testinvoke`命令(替换您自己的契约散列):

```
testinvoke 0x2b46bfe08185fbda2cb8121d6a2fd1a1d228c586 8
```

您应该会看到具有预期结果的输出:

```
----------------------------------------------------------------
Test invoke successful
Total operations: 19
Results [{'type': 'Integer', 'value': '9'}]
Invoke TX GAS cost: 0.0
Invoke TX fee: 0.0001
----------------------------------------------------------------
```

如果您收到一条关于找不到协定的消息，您可能需要等待一两分钟，让您的协定被挖掘到一个块中，然后才能调用它。同样，这将是一个本地调用，输入您的密码将在网络上真正运行合同，在此过程中向您收费。

# 后续步骤

在撰写本文时，Neo 文档相当零散。但是，以下一些网站提供了关于智能合同开发的良好信息:

*   [新 python 文档](https://neo-python.readthedocs.io/en/latest/index.html) ( [智能合约部分](https://neo-python.readthedocs.io/en/latest/neo/SmartContract/smartcontracts.html))
*   [Neo 文档](http://docs.neo.org/en-us/index.html) ( [智能合同部分](http://docs.neo.org/en-us/sc/introduction.html))

此外， [neon-js](https://github.com/CityOfZion/neon-js) 可以用于与 JavaScript 环境中的智能合约进行交互。

希望这个开发过程的基本演练能给你自己的项目一个起点，以帮助增强整个近地天体场景。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)