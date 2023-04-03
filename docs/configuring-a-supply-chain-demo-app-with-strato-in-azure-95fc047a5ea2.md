# 在 Azure 中使用斯特拉托配置供应链演示应用程序

> 原文：<https://medium.com/coinmonks/configuring-a-supply-chain-demo-app-with-strato-in-azure-95fc047a5ea2?source=collection_archive---------5----------------------->

Azure 中的 STRATO 工具套件允许开发人员加速与开发分散应用程序相关的各种流程和工作流。详情见:[*https://azure . Microsoft . com/en-us/block apps-strato-SMD-upgrade/*](https://azure.microsoft.com/en-us/blog/blockapps-strato-smd-upgrade/)

## **入门**

Azure Government 中的 Marketplace 包含一个资源模板，供开发人员生成一个生产环境，以一种简单的交钥匙方式开发、管理和支持企业级区块链应用程序。Azure Government 使用与商业 Azure 相同的底层技术，使您能够使用您已经熟悉的开发工具。

该快速入门指南将帮助您开始在 Azure Government 上实施**以太坊联盟**区块链网络。开发者也可以选择创建 *Hyperledger Fabric* 、 *Quorum* 和 *Corda* 区块链网络。还将发布这些平台的快速入门指南。

## **先决条件**

在配置示例之前，您必须具备以下条件:

*   一个活跃的 Azure 政府订阅。如果你没有 Azure 政府订阅，请在开始之前创建一个免费帐户。
*   访问 Ubuntu 终端以执行 CLI bash。如果在 Windows 10 上，检查一下 Windows 的 Linux 子系统:[https://msdn.microsoft.com/en-us/commandline/wsl/about](https://msdn.microsoft.com/en-us/commandline/wsl/about)

## **block apps 的背景**

这个 Azure 政府市场模板产品生成所有必要的计算、存储和网络组件，以创建一个私有以太网，在该网络上，您可以形成一个共享的全球分类帐状态，并在参与成员之间部署智能合同和交易。

…

## **在 Azure 中安装 STRATO 单节点产品**

要开始演示，用户必须配置一个 STRATO 区块链 single node-Developer Edition 资源。

*   在 web 浏览器中导航到 Azure 门户
*   单击左上角的“+”按钮创建新资源
*   在市场上搜索“STRATO Blockchain Singlenode 开发者版”
*   点击资源，并点击拉出式刀片中的“创建”

一旦选定，就该配置网络拓扑和基本设置了。模板部署将引导您完成网络配置。部署流程分为三个部分:基础、网络配置和以太坊配置。

1.在第一个刀片下，**基础知识**:

*   指定*资源前缀*、*虚拟机用户名*和*认证类型*的值(使用密码—或 SSH pubkey 以获得更高的安全性)。
*   对于资源组，请使用本指南开头配置的资源组，或者选择“新建”并指定参数名称的值。
*   对于位置，选择您希望托管虚拟机和网络的地理位置。

2.在'**虚拟机大小和性能'**刀片下:

*   选择存储性能和虚拟机大小。
*   对于本演示，默认配置选项应该没问题。

3.在'**层片下设置**叶片:

*   管理员密码设置将用于保护 STRATO 帐户

4.在下一个刀片中，您将看到指定输入的摘要。点击“确定”进入最后一个刀片

5.选择“创建”以部署 STRATO 节点。资源可能需要几分钟才能完成部署。

*   部署资源后，我们将收到通知，并应导航到资源组刀片以进行最近的部署。
*   导航到资源组后，选择“公共 IP 地址”资源(突出显示)。
*   复制列出的 IP 地址，并将其粘贴到浏览器的选项卡中
*   这里应该有一个要求输入用户名/密码的弹出窗口
*   用户名是“admin”
*   密码是您之前在模板中提供的值
*   如果成功，您应该会被带到 STRATO 管理仪表板门户

## **创建用户账户**

在 STRATO Management Dashboard 中，单击 accounts

接下来，单击屏幕右上角的“创建用户”

*   将用户名设为“test”
*   填写密码的值
*   要为新用户帐户提供资金，请点击帐户行左侧的蓝色“水龙头”按钮。

## **部署测试合同**

导航到 Contracts 选项卡，并尝试将一个示例合同部署到新资助的节点(有关测试合同，请参见 Ethereum 快速入门指南)。

## **安装一个 UBUNTU 16.04 LTS 虚拟机**

*   导航回 Azure 门户，搜索 Ubuntu Server 16.04 LTS 虚拟机
*   在基础刀片下配置各种虚拟机设置
*   确保将虚拟机放在部署斯特拉托单节点模板的同一个资源组中
*   使用默认配置设置完成接下来的两个刀片
*   安装虚拟机

## **SSH 进入虚拟机**

通过在资源组中查找新部署的虚拟机，导航到该虚拟机

*   打开它后，单击 connect 按钮(如图所示)。这将为您提供一个 ssh 命令，您可以使用该命令从终端连接到虚拟机
*   我们最终将需要两个终端窗口(一个运行 API，另一个运行应用程序本身)，所以请确保您 ssh 到 VM 两次

现在我们已经通过 SSH 连接到了虚拟机，运行这一系列命令:

```
$ sudo apt-get update
```

*   这将更新虚拟机

```
$ curl -sL [https://deb/.nodesource.com/setup_8.x](https://deb/.nodesource.com/setup_8.x) | sudo bash
```

*   这会拉动节点。JS 第 8 卷包

```
$ sudo apt-get install nodejs
```

*   这将安装节点。JS 第 8 卷包

```
$ sudo apt-get install build-essential$ Sudo apt-get install npm
```

接下来，克隆此回购:

```
$ git clone [https://github.com/blockapps/blockapps-ba.git](https://github.com/blockapps/blockapps-ba.git)
```

*   这是 BlockApps SupplyChain 演示应用程序的公共回购——包括源代码、JavaScript 等。安装应用程序

既然我们已经克隆了演示应用程序，我们需要安装依赖项并配置服务器。

导航到 blockapps 目录:

```
$ ls$ cd blocksapps-ba$ npm install$ cd ui$ npm install$ cd ..$ Cd server/config
```

*   使用文本编辑器编辑配置文件(如 Vim、nano 等)。)

```
$ nano 192.config.yaml
```

*   更新“节点”部分，以反映您的 strato 公共 IP 地址的公共 IP(在上一部分中复制)
*   更新“explorerUrl”、“stratoUrl”、“blocUrl”和“searchUrl”的 IP 地址

完成后，保存配置文件并导航回根目录。接下来，运行:

```
$ cd ..$ cd ..
```

**引导后端**

要启动 BA 供应链应用程序的引导后端，请输入:

```
$ SERVER=192 npm run deploy
```

现在，如果我们导航回 STRATO 管理仪表板并查看帐户，我们会发现角色(管理员、购买者、供应商等。)已经生成。通过导航到 contracts 选项卡，我们发现一系列合同(AdminInterface、ProjectManager 等。)已经生成。

既然后端已经部署好了，我们只需要启动它。为此，导航回终端并运行:

```
$ npm run start
```

*   这将返回一堆信息，并以“监听端口 3031”结束

**开放港口**

*   在 Azure 门户中导航到你的 Ubuntu 虚拟机
*   点击设置->网络->添加入站端口规则
*   添加 IP 端口 3030(用于 App UI)和 3031(用于 API)
*   默认情况下，斯特拉托节点上的所有端口都是预配置的

将 Ubuntu 框指向 API

*   抢 Ubuntu 盒子的公共 IP

打开一个新的 putty 会话，运行:

```
$ cd blockcapps-ba/ui$ API_URL=http://<public-ip_of_ubuntu_VM>:3031 npm run start
```

## 导航到应用程序

要导航到该应用程序，请在浏览器的 URL 中输入端口为 3031 的 ubunut 虚拟机的公共 ip。

```
http://<public-ip_of_ubuntu_VM>:3031
```

这将把我们带到应用程序的登录界面，输入用户名“Buyer1”和密码“1234”(在服务器配置文件中配置)。一旦我们登录，我们可以输入一个新项目的请求，并允许供应商对它进行投标。请参见下面的示例项目屏幕。

如果我们现在注销，并作为供应商重新登录(用户名:供应商 1，密码:1234)。我们可以看到我们可以投标的开放项目。

我们可以通过作为供应商参与投标来投标该项目。让我们为这批试运货物出价 400 美元。

如果我们作为买家重新登录，我们可以接受或拒绝出价。希望这能给我们一个很好的例子，展示在区块链上运行的使用供应链的示例应用程序可能是什么样子。使用这个示例应用程序，我们可以轻松地在斯特拉托管理仪表板中可视化所有合同数据，而不必使用 geth 等。