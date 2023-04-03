# 安装闪电网络第 2 部分:又来了

> 原文：<https://medium.com/coinmonks/installing-lightning-network-part-2-here-we-go-again-ed5a84f9cade?source=collection_archive---------0----------------------->

![](img/fc72a1be84010acf753b3ef176ff814a.png)

[image source](https://www.google.co.in/url?sa=i&source=images&cd=&cad=rja&uact=8&ved=2ahUKEwjYvsSh4eHcAhVPVH0KHZ6JCbEQjxx6BAgBEAI&url=http%3A%2F%2Fbandwidthblog.com%2Fcryptocurrency%2Flightning-network%2F&psig=AOvVaw0RMeGCKoZSF5iFTDTE3kST&ust=1533965522185054)

在[第一部分](/coinmonks/the-lightning-network-how-to-install-and-hopefully-make-money-6e3058e3fa7c)中，我在[数字海洋](https://m.do.co/c/b33858702e30)水滴上安装了一个闪电节点。目标是探索闪电网络，看看是否有可能产生被动收入。通过在服务器上全天候运行 Lightning node，并将付款从一个节点转发到另一个节点，就可以赚钱。您的节点连接得越好，通过您的节点传送的变更付款就越高。那么第一个月过得怎么样？

# 一个月后

虽然其他节点让[一周赚了 5%](https://www.marc.cn/2018/06/the-lightning-network-a-huge-opportunity-to-learn-and-earn-money.html)，但至少在很短的一段时间内，我发现我根本没有赚到钱。没有。我有大约 85 个到其他节点的通道，但是没有一个事务是通过我的节点路由的。

我遇到的第二个问题是，我无法连接到其他一些节点。最后，我注意到我创建的一些频道处于停滞状态，为此我在 GitHub 上开了一期。

所以我创建了第二个节点来仔细检查我的节点是否工作正常。鉴于我在创建和资助渠道方面没有问题，我不认为会有什么大问题。然而，事实证明我的节点有严重的问题。我几乎解决了(大部分)问题。这是一次既令人兴奋又令人沮丧的旅程。

设置第二个节点后，我发现的主要问题是我的两个节点无法相互连接，即使它们运行完全相同的软件。我为连接问题单独开了[一期 GitHub。](https://github.com/ElementsProject/lightning/issues/1798)

长话短说:Lightning 端口设置不正确，Lightning 软件无法检测到这个问题。更糟糕的是，C-Lightning readme 上的示例设置配置包含完全相同的错误:Lightning 端口没有将传入数据转发到 Lightning Docker 容器。

好消息是，我确实找到了一种使用 Docker Compose 安装 Lightning 节点的更简单的方法。我还修改了 [Lightning 自述文件](http://github.com/ElementsProject/lightning/)并创建了一个拉取请求。下面介绍如何*正确*设置闪电节点:

# 从第 1 部分升级

如果你按照第 1 部分[中的说明安装你的闪电节点](/coinmonks/the-lightning-network-how-to-install-and-hopefully-make-money-6e3058e3fa7c)，你将需要移除 docker 容器并移除配置文件。

1.  移除现有的码头集装箱
2.  删除 bitcond 和 clightning 配置文件
3.  删除便捷访问脚本

> 您不需要(也不想)删除 droplet，因为这将迫使您恢复备份，这是一个困难且有风险的过程(详见第 1 部分)。

首先，登录您的 droplet。

```
# Log in.
$ **ssh root@**<droplet's ip-address> **-i ~/.ssh/lightning**
```

接下来，删除现有的 con

```
# Stop and remove the existing docker containers# 1\. Find the docker container id's.
#    The -a argument also lists paused containers
$ **docker ps -a**# 2\. Stop the containers, using the id's found in step 1.
#    Repeat this step for each container.
$ **docker stop** <container id># 3\. Remove the containers.
#    Repeat this step for each container.
$ **docker rm** <container id>
```

接下来，我们将删除比特币和照明配置文件

```
# 1\. Remove the bitcoin config file.
$ **cd** **/scratch/bitcoin/mainnet/bitcoind**
$ **rm bitcoin.conf**# 2\. Remove the lightning config file.
$ **cd** **/scratch/bitcoin/mainnet/clightning**
$ **rm config**
```

并删除方便脚本。

```
# 1\. Remove the bitcoin convenience script.
$ **rm /bin/bitcoin-cli**# 2\. Remove the lightning convenience script.
$ **rm** **/bin/lightning-cli**
```

你现在可以重新安装比特币和闪电了。跳过全新安装章节，继续章节(重新)安装比特币和闪电。

# 全新安装

如果您没有使用第 1 部分中解释的步骤[安装 Lightning 节点，您将需要使用第 1 部分中的以下章节来设置您的节点:](/coinmonks/the-lightning-network-how-to-install-and-hopefully-make-money-6e3058e3fa7c)

*   建立数字海洋

然后，登录您的 droplet。

```
# Log in.
$ **ssh root@**<droplet's ip-address> **-i ~/.ssh/lightning**
```

接下来，我们将为 bitcoind 和 clightning 创建工作目录:

```
# 1\. Create a working directory outside of the Docker image,
#    so the working directory won't be overwritten when you 
#    update the Docker image with a new bitcoind version.
$ **mkdir -p /scratch/bitcoin/mainnet/bitcoind**# 2\. Create a working directory outside of the Docker image,
#    so the working directory won't be overwritten when you 
#    update the Docker image with a newer C Lightning version.
$ **mkdir -p /scratch/bitcoin/mainnet/clightning**
```

# (重新)安装比特币和闪电

所以有一个

1.  设置防火墙
2.  使用 docker-compose 创建 Docker 容器
3.  创建便捷访问脚本

数字海洋水滴带有一个名为 ufw 的防火墙。我们将为进出流量开放端口 8333(比特币)和 9735(闪电)。

```
# 1\. Open bitcoin port 8333.
$ **sudo ufw allow 8333**# 2\. Open lightning port 9735.
$ **ufw allow 9735**
$ **ufw allow out 9735**# 3\. Check the firewall status.
$ **ufw status**
```

如果一切正常，ufw 将报告类似于下面列表的内容(加上任何其他打开的端口)。

```
To                         Action      From
--                         ------      ----
9735                       ALLOW       Anywhere
8333                       ALLOW       Anywhere
9735 (v6)                  ALLOW       Anywhere (v6)
8333 (v6)                  ALLOW       Anywhere (v6)
9735                       ALLOW OUT   Anywhere
8333                       ALLOW OUT   Anywhere
9735 (v6)                  ALLOW OUT   Anywhere (v6)
8333 (v6)                  ALLOW OUT   Anywhere (v6)
...
```

接下来，我们将创建 bitcoind 和 clightning 容器。与第 1 部分不同，这次我们将使用一个更容易使用的 docker-compose 脚本。

```
# 1\. Create an empty docker-compose file in nano.
$ **cd ~**
$ **nano docker-compose.yml**# 2\. Copy and paste the docker-compose.yml code below
#    and change the aliasname, rbg, and announce-addr# 3\. Save the edited file (Ctrl-X, Y, <Enter>)
```

docker-compose.yml 的内容如下所示。将`alias`设置为您选择的节点名称，将`announce-addr`设置为 droplet 的 ip 地址，并可选地将`rgb`值设置为将用于在 Lightning explorers 中显示您的节点的颜色。

```
**version: "3"
services:
  bitcoind:
    image: nicolasdorier/docker-bitcoin:0.16.0
    container_name: bitcoind
    environment:
      BITCOIN_EXTRA_ARGS: |
        testnet=0
        whitelist=0.0.0.0/0
        server=1
        rpcuser=rpcuser
        rpcpassword=rpcpass
    expose:
      - "8333"
    ports:
      - "0.0.0.0:8333:8333"
    volumes:
      - "/scratch/bitcoin/mainnet/bitcoind:/data"** **clightning_bitcoin:
    image: elementsproject/lightningd
    container_name: lightning
    command:
      - --bitcoin-rpcconnect=bitcoind
      - --bitcoin-rpcuser=rpcuser
      - --bitcoin-rpcpassword=rpcpass
      - --network=bitcoin
      - --alias=**<Lightning node name>
      **- --rgb=FF4500
      - --log-level=debug
      - --announce-addr=**<droplet IP-address>**:9735
    environment:
      EXPOSE_TCP: "true"
    expose:
      - "9735"
    ports:
      - "0.0.0.0:9735:9735"
    volumes:
      - "/scratch/bitcoin/mainnet/clightning:/root/.lightning"
      - "/scratch/bitcoin/mainnet/bitcoind:/etc/bitcoin"
    links:
      - bitcoind**
```

> 注意:docker-compose.yml 文件基于 [C-Lightning 自述文件](https://github.com/ElementsProject/lightning/blob/master/README.md)中的示例。不幸的是，最初的自述文件包含一个错误:容器没有公开端口。我已经创建了包含修复的[拉请求#1816](https://github.com/ElementsProject/lightning/pull/1816) 。

使用 docker-compose 创建并运行容器:

```
$ **docker-compose up**
```

Docker 将创建并运行两个容器。bitcoind 容器应该立即开始下载区块链。对于全新安装，这将需要几个小时。

由于脚本不会退出，请打开一个新的终端窗口或标签，然后再次登录您的 droplet。

```
# Log in.
$ **ssh root@**<droplet's ip-address> **-i ~/.ssh/lightning**
```

添加一个方便的脚本来访问 bitcoind 容器:

```
# 1\. Create script and edit script (see below).
$ **nano /bin/bitcoin-cli**# 2\. Copy and past script below# 3\. Save the edited file (Ctrl-X, Y, <Enter>)# 4\. Make script executable.
$ **chmod +x /bin/bitcoin-cli**# 4\. Confirm script is working. This command shows the wallet info.
$ **bitcoin-cli getwalletinfo**
```

在 Nano(上面的步骤 3)中，编辑比特币 cli 脚本，如下所示:

```
**#!/usr/bin/env bash
docker exec lightning bitcoin-cli -rpcuser=rpcuser -rpcpassword=rpcpass "$@"**
```

为 Lightning 添加一个方便的脚本

```
# 1\. Create and edit script (See below).
$ **nano /bin/lightning-cli**# 2\. Copy and paste the script below# 3\. Save the edited file (Ctrl-X, Y, <Enter>)# 2\. Make script executable.
$ **chmod +x /bin/lightning-cli**# 4\. Confirm script is working. 
$ **lightning-cli getinfo**
```

在 Nano 中(上面的步骤 2)，按如下方式编辑 lightning-cli 脚本:

```
**#!/usr/bin/env bash
docker exec lightning lightning-cli "$@"**
```

仅此而已。享受你的全功能闪电节点！

> 实际上，不完全是。您将看到比特币命令行界面脚本不起作用。由于您不会经常使用它，所以我将在这里保留已损坏的脚本。如果你看到哪里有问题，请留下评论，我会更新帖子。

# 更多更好的连接

在[第 1 部分](/coinmonks/the-lightning-network-how-to-install-and-hopefully-make-money-6e3058e3fa7c)中，我列出了几种找到要连接的节点的方法。在我发布了这篇文章之后，我遇到了 [Moneni，它可以找到你的节点没有连接到的高度连接的节点。](https://www.moneni.com/nodematch)

# 错误和解决方案

虽然我解决了其他节点无法连接到我的节点的最大问题，但是还有我在[第 1 部分](/coinmonks/the-lightning-network-how-to-install-and-hopefully-make-money-6e3058e3fa7c)中提到的尚未解决的主要问题:

## 通道等待锁定和通道关闭

即使一个月后，一些频道仍然停留在`awaiting lockin`或`shutting down`状态。我已经打开了一个问题( [issue #1776](https://github.com/ElementsProject/lightning/issues/1776) )，但是还没有收到任何评论反馈。这些渠道的比特币已经无法访问一个多月了。

# 最后

我将运行服务器几个星期，并会分享如果运行一个闪电节点实际上是有利可图的。

如果你正在运行一个闪电节点，请考虑创建一个渠道给我，并资助该渠道。我很感激！

```
$ **lightning-cli connect 02060d0c8577cfc2a1962220a1cde4f623ca55ef80bd25e4be5a8b15fed78b11a3@178.62.237.239**
```

# 谢谢

非常感谢里奇·米勒[、](https://twitter.com/richardkazuo_en)[阿瑟尼·耶雷明](https://medium.com/u/868945ad81ef?source=post_page-----ed5a84f9cade--------------------------------)、[大卫·卡特](https://medium.com/u/13184ff3ef9e?source=post_page-----ed5a84f9cade--------------------------------)、[李畅](https://twitter.com/BTCicecream)以及 C-Lightning 团队。

*需要一个数字海洋账户？按照这个推荐链接到* [*在数字海洋上创建一个帐户，并收到 10 美元*](https://m.do.co/c/b33858702e30) *(我将收到 25 美元)。*

*如果你喜欢这篇文章，并想阅读更多或表达你的欣赏，比特币总是在:3 badhnwyuk 54 zva 1 drj 6 jj 8 H2 bjz 63 RC*

![](img/a8ac6a9c6b65c47cd72c02a22b804bdc.png)