# 使用 ansible 在 AWS 上部署 hyperledger-fabric

> 原文：<https://medium.com/coinmonks/deploying-hyperledger-fabric-on-aws-using-ansible-c2b240eac83f?source=collection_archive---------0----------------------->

Hyperledger-fabric 是一个模块化的区块链架构，允许开发人员开发应用程序来解决他们的业务用例。该框架的使用案例是多样化的，不仅限于 ICO 或加密货币。然而，由于结构体系结构的复杂性，许多人在部署结构体系结构时遇到了问题。

其中一个原因是，由于 fabric 严重依赖 docker，像我这样愿意实现该架构的许多开发人员并不太了解 docker，或者对它的了解有限或很少。

另一个原因是 hyperledger-fabric 是区块链许可的，所以你不会发现太多基于这个架构的展示应用，不像以太坊是公共区块链。

写这篇文章的目的不是解释 hyperledger fabric 的基本架构，而是关注部署阶段，因为这是大多数人被困的地方。

在这个系列文章中，我将讨论以下主题

1-在多个 aws 服务器上部署 hyperledger 结构

2-将 node-sdk 等客户端与现有网络集成，这样您就可以在 sdk 基础上构建 web 应用程序

**先决条件:**

1-您必须对 hyperledger-fabric 体系结构及其工作原理有基本的了解

1-您必须有一个 aws 帐户。

2-你应该有一台 ubuntu 机器。

3- Git 应该安装在本地系统上。如果您还没有安装它，您可以通过键入以下命令来安装它

> (sudo apt 安装 git)

**在 AWS 上设置面料:**

为了在 aws 上部署，我们将使用 ansible 脚本。Ansible 是一个自动化工具，它可以在任何特定的服务器上执行一组预定义的任务，因此它不会手动配置每个服务器，而是会针对 ansible 配置文件中定义的特定服务器自动运行一些命令。

为了让 ansible 工作，我们需要在我们的本地机器上配置它，我们通常称之为 ansible controller。

**在本地机器上配置 ansi ble:**

虽然 ansible 配置非常简单，并且在 hyperledger-cello docs([https://github . com/hyperledger/cello/tree/master/src/agent/ansi ble](https://github.com/hyperledger/cello/tree/master/src/agent/ansible))上有很好的记录，但是我将在这里写下所有的命令。

首先通过键入以下命令克隆官方 hyperledger-cello 库。

> git 克隆[https://github.com/hyperledger/cello.git](https://github.com/hyperledger/cello.git)

现在要在本地机器上安装 ansible，运行下面的命令。它将安装 ansible 工作所需的所有软件包

```
sudo apt-get update
sudo apt-get install python-dev python-pip libssl-dev libffi-dev -y
sudo pip install --upgrade pip
sudo pip install 'ansible>=2.3.0.0'
```

现在你需要在你的系统中做的最后一件事就是安装云相关的包。如果您正在使用 aws，则需要安装 awsboto 如果您正在使用 openstack，则需要安装 OpenStack shade。因为我们关注 aws 部署，所以我们将在本地机器上安装 aws 依赖包。运行以下命令来安装 aws boto。它既可以从 apt 软件包管理器安装，也可以从 pip 安装。

```
`sudo pip install -U boto
 sudo pip install -U boto3
```

这里-U 标志代表升级，所以如果你已经安装了这些软件包，它将升级到最新的。

现在导航到 cello 中的一个可行目录

我将尝试解释与 aws 相关的主要目录的用途，以便您可以根据您的用例/场景轻松配置

主 ansible 目录包含几个配置文件，如 aws.yml、azure.yml，这些文件是针对特定环境的。您不需要更改这些配置文件。

如果你看一下 aws.yml 文件([https://github . com/hyperledger/cello/blob/master/src/agent/ansi ble/AWS . yml](https://github.com/hyperledger/cello/blob/master/src/agent/ansible/aws.yml))，它包含了正确设置 fabric 需要运行哪些任务的说明。

与 aws 相关的另一个配置文件是`vars``目录下的 AWS . yml([https://github . com/hyperledger/cello/tree/master/src/agent/ansi ble/vars](https://github.com/hyperledger/cello/tree/master/src/agent/ansible/vars))，需要稍加改动。让我们看一下配置文件

```
---
# AWS Keys will be use to provision EC2 instances on AWS Cloud
auth: {
  auth_url: "",
  # This should be your AWS Access Key ID
  username: "AKIAJCA47Q46DOT2M7WA",
  # This should be your AWS Secret Access Key
  # can be passed as part of cmd line when running the playbook
  password: "{{ password | default(lookup('env', 'AWS_SECRET_KEY')) }}"
}# These variable defines AWS cloud provision attributes
cluster: {
  region_name: "us-east-1",     #TODO  Dynamic fetch
  availability_zone: "", #TODO  Dynamic fetch based on region
  security_group: "Fabric",target_os: "ubuntu",
  image_name: "ubuntu/images/hvm-ssd/ubuntu-xenial-16.04-amd64-*",
  image_id: "ami-d15a75c7",
  flavor_name: "t2.medium",  # "m2.medium" is big enough for Fabric
  ssh_user: "ubuntu",
  validate_certs: True,
  private_net_name: "demonet",public_key_file: "/home/mobaid/.ssh/id_rsa.pub",
  private_key_file: "/home/mobaid/.ssh/id_rsa",
  ssh_key_name: "finalmvp",
  # This variable indicate what IP should be used, only valid values are
  # private_ip or public_ip
  node_ip: "public_ip",container_network: {
    Network: "172.16.0.0/16",
    SubnetLen: 24,
    SubnetMin: "172.16.0.0",
    SubnetMax: "172.16.255.0",
    Backend: {
      Type: "udp",
      port: 8285 
    }
  },service_ip_range: "172.15.0.0/24",
  dns_service_ip: "172.15.0.4",# the section defines preallocated IP addresses for each node, if there is no
  # preallocated IPs, leave it blank
  node_ips: [ ],# fabric network node names expect to be using a clear pattern, this defines
  # the prefix for the node names.
  name_prefix: "fabric",
  domain: "fabricnet",# stack_size determines how many virtual or physical machines we will have
  # each machine will be named ${name_prefix}001 to ${name_prefix}${stack_size}stack_size: 3,etcdnodes: ["fabric001", "fabric002", "fabric003"],
  builders: ["fabric001"],flannel_repo: "[https://github.com/coreos/flannel/releases/download/v0.7.1/flannel-v0.7.1-linux-amd64.tar.gz](https://github.com/coreos/flannel/releases/download/v0.7.1/flannel-v0.7.1-linux-amd64.tar.gz)",
  etcd_repo: "[https://github.com/coreos/etcd/releases/download/v3.2.0/etcd-v3.2.0-linux-amd64.tar.gz](https://github.com/coreos/etcd/releases/download/v3.2.0/etcd-v3.2.0-linux-amd64.tar.gz)",
  k8s_repo: "[https://storage.googleapis.com/kubernetes-release/release/v1.7.0/bin/linux/amd64/](https://storage.googleapis.com/kubernetes-release/release/v1.7.0/bin/linux/amd64/)",go_ver: "1.8.3",# If volume want to be used, specify a size in GB, make volume size 0 if wish
  # not to use volume from your cloud
  volume_size: 8,# cloud block device name presented on virtual machines.
  block_device_name: "/dev/vdb"
}
```

首先你需要在认证部分改变`username`。您需要从您的 aws 帐户生成 AWS 访问密钥 Id 和 AWS 秘密访问令牌。一旦生成了这些密钥，就用您的密钥替换那个`AWS Access Key Id`。关于您的秘密访问令牌，请将其保存在其他地方。你以后会需要这把钥匙。

**集群部分:C** 集群部分定义你的区域名称和安全组。当您运行 ansible 脚本时，它将在特定区域创建集群，并创建名为`Fabric`的安全组。如果您想更改安全组名称，您可以在此文件中进行更改。

**Target_os 部分**:这个部分定义了应该在 amazon 集群上安装什么操作系统。有一件事你需要确保你的 aws 帐户应该能够托管`t2.medium`实例。正常情况下，免费的 aws 帐户有`t2.micro`个实例的限制，这对于运行光纤网络来说是不够的。我会告诉你原因，在这篇文章的后面，当我来解释织物布局。

下一部分与公钥/私钥部分相关。Ansible 会将公钥和私钥存储在您的本地 ssh 目录中，这样以后您就可以从您的终端 ssh 到您的 ubuntu 实例中。你需要注意的一件事是`ssh_key_name``。你应该从你的 aws 账户这里【https://console.aws.amazon.com/ec2/v2/home? 生成并下载你的 ssh 密钥对 region = us-east-1 # key pairs:sort = keyName 并用您的密钥对名称替换`ssh_key_name`’。当登录 ubuntu 实例时，您需要提供 ssh 密钥路径。

**Stack_Size** : Stack_size 定义您想要放入 fabric 架构中的服务器数量。默认情况下，ansible 使用 3 台服务器，但您可以根据自己的需求增加或减少它。

**配置织物布局:**

在这一部分中，我们将配置 fabric layout，它基本上说明了我们的服务器中将有多少组织、对等体和订购服务以及它们的组合。Ansible 默认使用此配置文件在 aws 上部署 fabric。([https://github . com/hyperledger/cello/blob/master/src/agent/ansi ble/vars/bc1st . yml](https://github.com/hyperledger/cello/blob/master/src/agent/ansible/vars/bc1st.yml))

让我们来看看这个文件

> — -
> #结构源库的 URL
> GIT _ URL:"[http://gerrit.hyperledger.org/r/fabric](http://gerrit.hyperledger.org/r/fabric)"
> 
> # gerrit 补丁集引用应由 GERRIT 自动设置
> # GERRIT _ ref spec:" refs/changes/23/11523/3 " # 1 . 0 . 0
> # GERRIT _ ref spec:" refs/changes/47/12047/3 " # 1 . 0 . 1
> GERRIT _ ref spec:" refs/changes/13/13113/1 "
> 
> #此变量定义结构网络属性
> 结构:{
> 
> #连接到服务器的用户
> ssh_user: "ubuntu "，
> 
> #选项为" goleveldb "，" CouchDB "，默认为 goleveldb
> peer_db: "CouchDB "，
> tls: false，
> 
> #以下部分定义了光纤网络将如何组成
> # cas 表示认证机构容器
> # peers 表示对等容器
> # orders 表示订购方容器
> # kafka 表示 kafka 容器
> #所有名称都必须是小写。数字字符不能用于开始
> #或结束名称。字符点(。)只能用于同行和订购者的名称中。
> 网络:{
> fabric 001:{
> cas:[" ca1st . orga "]，
> 同行:["anchor@peer1st.orga "，" anchor@peer1st.orgb"]，
> 订购者:[" order 1 ST . orgc "，" order 1 ST . orgd "]，
> 动物园管理员:["zookeeper1st"]，
> 卡夫卡:["kafka1st"]
> }，【T18
> 
> baseimage_tag: "1.0.2 "，
> ca: { tag: "1.0.2 "，admin: "admin "，adminpw: "adminpw" }
> }

这个布局文件告诉 ansible 在三个 ubuntu 服务器上部署 4-organizations 和他们的同行，数据库设置为`couchdb``。在 4 个组织中，orgc 和 orgd 充当订购服务，而 orga 和 orgb 将是涉及资产转移的主要参与者。

因为有多个订购服务，所以我们需要 kafka 和 zookeeper 服务器的共识机制。在这个布局文件中，我们定义了 3 个 kafka 和 zookeeper 服务器，对于这个网络来说足够了。但是，对于较大的网络，您应该正确计算这些 kafka 服务器的规模。您可以定义尽可能多的 kafka 服务器，但要确保它不会过度破坏您的应用程序。

在这篇文章的上面，我提到过`t2.micro`实例不足以运行这个 fabric 布局。原因是 zookeeper 和 Kafka 需要至少 1 GB 内存才能正常工作，而 t2.micro 实例无法提供。如果你运行微实例，你可能会在 kafka 容器中得到`Memory corruption`错误。

文件中的其余内容不言自明。如果您对这个配置文件有任何疑问，请告诉我。

**其他重要文件:**

`**ansible/role/deploy_compose**``:该文件夹包含从 channel_artifacts 到 instantiate_chaincode 关于 fabric 设置的所有必要代码。

**Chaincode** :如果您想要更改 Chaincode，请导航到[https://github . com/hyperledger/cello/tree/master/src/agent/ansi ble/roles/deploy _ compose/fabric setup/templates](https://github.com/hyperledger/cello/tree/master/src/agent/ansible/roles/deploy_compose/fabricsetup/templates)并将您的 chaincode 粘贴到文件`firstcode.go`中

**背书策略:**如需更改背书策略，导航至`dochannel.j2``([https://github . com/hyperledger/cello/blob/master/src/agent/ansi ble/roles/deploy _ compose/fabric setup/templates/doc hannel . J2](https://github.com/hyperledger/cello/blob/master/src/agent/ansible/roles/deploy_compose/fabricsetup/templates/dochannel.j2))，根据您的业务需求更改策略。

修改完所有这些内容后，从 ansible 目录运行脚本，如下所示。

导出您之前保存的 AWS 秘密访问密钥

> `export AWS_SECRET_KEY="your secret key of your aws account"`

下一次运行 ansible 命令

> ansi ble-playbook-e " mode = apply " AWS . yml

根据您的互联网连接，此命令可能需要一段时间。

当命令正确完成时，您将得到如下输出

![](img/252f9f3b8496589bc34f4a4e4105d2a3.png)

**调用查询:**

现在，为了测试我们部署的 fabric 服务器是否正常工作，我们将调用一些查询，看看每个对等点是否得到相同的结果..

你应该在这里看到你所有正在运行的实例[https://console.aws.amazon.com/ec2/v2/home?region = us-east-1 # Instances:sort = instanceState](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:sort=instanceState)

现在使用您的 ssh 密钥登录到这些实例，您应该看到一些 docker 容器根据您的 fabric 布局运行。

因为没有客户端与这个网络集成，所以您需要从对等容器调用查询。通过键入以下命令转到对等容器

> 码头工人的狂欢

对于频道列表

> 对等提取通道-o <orderer container="" name="">:7050</orderer>

您应该得到通道名`firstchannel`’,因为 ansible 在默认情况下使用该名称创建通道。

要从对等终端调用事务，请运行以下命令

```
peer chaincode invoke -o <orderer container name> -C <channel_name> -n <chaincode name> -c '{"Args":["invoke","a","b","10"]}'
```

这将调用事务并将`10`从`a`转移到`b`

要检查`a`的平衡，运行以下命令。

```
peer chaincode query -C <channel name> -n <chaincode name> -c '{"Args":["query","a"]}'
```

您可以用类似方式在其他服务器上检查结果。如果您得到相同的结果，这意味着您的结构网络在多个服务器上正常工作，并且所有对等点和订购服务都相互连接。

在下一篇文章中，我们将把 node-sdk 这样的客户端与这个现有的网络集成起来，并创建公共 api，这样您就可以从任何地方通过调用它们的 api 来运行基本的区块链命令。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)