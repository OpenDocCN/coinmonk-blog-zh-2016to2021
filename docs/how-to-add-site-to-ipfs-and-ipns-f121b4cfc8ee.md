# 如何将网站添加到 IPFS 和 IPNS

> 原文：<https://medium.com/coinmonks/how-to-add-site-to-ipfs-and-ipns-f121b4cfc8ee?source=collection_archive---------0----------------------->

IPFS 允许在网络应用程序和简单的网页中放置 p2p 内容。你也可以在点对点(p2p) IPFS 网络托管整个静态网站。因此，其他人，谁拥有 IPFS 节点可以分享你的网站，并成为你的内容和分发 HTML，CSS，JS 和图片从他们的节点对等。之后——如果你的节点停止了，这个站点将会继续存在，而拥有 IPFS 节点的人将会在他的节点上保留一个站点。

> [发现并回顾最佳区块链软件](https://coincodecap.com)

## 如何在 IPFS 网络中创建您的第一个网站:

1.  运行你自己的 IPFS 节点
2.  通过简单的命令向 IPFS 添加内容
3.  使用结果内容散列(命令的输出)通过链接获取文件:`https://ipfs.io/ipfs/<your hash here>`
4.  你可以将这个链接放在 img 标签中，例如:`<img src=”https://ipfs.io/ipfs/<your hash here>”/>` 或者你可以将网站发布到 https://ipfs.io/ipns/my-site.url 的 IPNS，并通过链接:
    进行查看

![](img/6675b012a3f2b2d4a75465a53d31ce32.png)

现在让我们详细描述这 4 点:

## 1.下载并运行 IPFS 节点:

[https://dist.ipfs.io/#go-ipfs](https://dist.ipfs.io/#go-ipfs)
让我们以 linux amd64 发行版为例:

```
cd ~/
wget [https://dist.ipfs.io/go-ipfs/v0.4.14/go-ipfs_v0.4.14_linux-amd64.tar.gz](https://dist.ipfs.io/go-ipfs/v0.4.14/go-ipfs_v0.4.14_linux-amd64.tar.gz)
```

然后按照这里的说明安装它:
[https://ipfs.io/docs/install/](https://ipfs.io/docs/install/)
在我的例子中:

```
tar xvfz go-ipfs_v0.4.14_linux-amd64.tar.gz
cd go-ipfs
./install.sh
```

现在，您可以初始化并运行节点:

```
ipfs init
ipfs daemon > ipfs.log &
```

## 2.向 IPFS 节点添加内容:

让我们创建您的 IPFS 网站目录:

```
cd ~/
mkdir my-site; cd my-site
```

现在，举个例子，你可以下载一些猫的图片:

```
wget -O cat.jpeg [https://images.pexels.com/photos/104827/cat-pet-animal-domestic-104827.jpeg?auto=compress&cs=tinysrgb&h=350](https://images.pexels.com/photos/104827/cat-pet-animal-domestic-104827.jpeg?auto=compress&cs=tinysrgb&h=350)
```

![](img/4ef33c8a8352caac275d9ac54ad0cdc5.png)

cat.jpeg

并使用以下内容创建 index.html 和 style.css:

最后，将整个站点目录添加到您的节点:

```
cd ../
ipfs add -r my-site/
```

您应该会看到类似这样的内容:

```
added QmQiuSt4dKVFvi1zkYbrBWgX8CpasLmzux6dWrdfoDq1Qc my-site/cat.jpegadded QmQtJm6VcLdyMa6RqWESPgXsqmiaJ48RXrLFXojPPtHkni my-site/index.htmladded QmZL2UBTwnhcLv66fARL9UV8W8a9ZA4iwTLcaUCsB1u1yW my-site/style.cssadded QmNUhKfcGJyQJnZu3AKn8NoiomDwDCRBicgqPt1YRqJBCz my-site
```

## 3.访问您的网站由 IPFS 哈希

最后一行的散列是你的站点的根，你可以通过打开[https://ipfs.io/ipfs/](https://ipfs.io/ipfs/)你的散列这里>来访问它。因此示例站点位于[https://ipfs . io/ipfs/qmnuhkfcgjyqjnzu 3 akn 8 noiomdwddcrbicgqpt 1 yrqjbcz](https://ipfs.io/ipfs/QmNUhKfcGJyQJnZu3AKn8NoiomDwDCRBicgqPt1YRqJBCz)

你可以通过链接获得任何你添加到 IPFS 的文件。

## 4.发布到 IPNS

尽管您可以获得一个指向该站点的链接，但当您对其进行更改时，该链接仍将指向该站点的以前版本。这是因为 IPFS 使用类似 git 的文件哈希系统(哈希指向文件的特定版本，如提交哈希)。所以使用这个 IPFS 链接，你将无法获得该网站的新版本。

这就是为什么我们需要 IPNS——将您的目录或文件绑定到您的节点 peerID。之后——当使用 IPNS 链接时，我们可以随时获得网站的最新版本。

现在，我们想将网站的当前版本发布到 IPNS:

```
ipfs name publish QmNUhKfcGJyQJnZu3AKn8NoiomDwDCRBicgqPt1YRqJBCz
```

您应该会看到类似这样的内容:

```
Published to QmYmmfn68vkcFDeZz1NTZyEXTixjjUnUS6UaPdMSsUBWxs: /ipfs/QmNUhKfcGJyQJnZu3AKn8NoiomDwDCRBicgqPt1YRqJBCz
```

这将返回您的 peerID(qmymmfn 68 vkcfdezz 1 ntzhyextixjjunus 6 uapdmssubwxs)和您要发布到 peerID 的散列。您可以通过运行以下命令进行确认

```
ipfs name resolve <peerID>
```

之后——你可以通过链接访问网站的发布版本:https://ipfs.io/ipns/

在我的例子中:[https://ipfs . io/ipns/qmymmfn 68 vkcfdezz 1 ntzhyextixjjunus 6 uapdmssubwxs](https://ipfs.io/ipns/QmYmmfn68vkcFDeZz1NTZyEXTixjjUnUS6UaPdMSsUBWxs)

将来，当您需要更改站点内容时，您可以这样做，然后将新的站点版本添加到 ipfs 并再次发布它:

```
ipfs add -r my-site/
ipfs name publish <new site hash>
```

新的站点散列将会附加到你的 peerId 上，当用户访问 IPNS 链接时，他将会得到站点的最新版本。

但是这种 IPNS 链接仍然太长，而且对人类不友好。这就是我增加加分的原因:

## 5.将 IPNS 绑定到域

为此，你需要[购买一个域名](http://godaddy.com)，然后更改域名 DNS 设置。

您可以通过添加以下形式的 DNS TXT 记录将 IPNS 绑定到域

“dnslink=/ipfs/ <peerid>”，因此您可以使用类似/ipns/example . com/2015/09/15/hosting-a-website-on-ipfs/的路径。
例如，我把我的网站添加到了域名系统，现在可以通过网址[https://ipfs.io/ipns/cat.jonybang.ru/](https://ipfs.io/ipns/cat.jonybang.ru/)进入</peerid>

## 附言

如果你或我的内容加载太慢，请不要担心。IPFS 的一个优势是内容越受欢迎，加载速度越快。因为更多的同行分享你的内容，客户获得更多的来源，你的内容变得更快。

为了提高内容的加载速度——请朋友分享您的内容，我采用以下步骤:

```
cd ~/ && mkdir ipfs-files # Create folder to download ipfs content
cd ipfs-files
ipfs get /ipns/cat.jonybang.ru
ipfs add ./cat.jonybang.ru
```

如果您将我的示例站点和其他 IPFS 站点添加到您的 ipfs 节点，您将帮助我和所有星际文件系统(IPFS)社区！

## PPS

要在 IPFS 节点上创建多个站点，您可以生成几个命名的 peerId。例如添加新的狗站点:

```
ipfs key gen --type=rsa --size=2048 dog-site
ipfs add ./my-dog-site
ipfs name publish --key=dog-site <hash of dog folder>
```

在推特上关注我:https://twitter.com/jony_bang

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [电网交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   [Bitget 回顾](https://coincodecap.com/bitget-review)|[Gemini vs block fi](https://coincodecap.com/gemini-vs-blockfi)|[OKEx 期货交易](https://coincodecap.com/okex-futures-trading)
*   [美国最佳加密交易机器人](https://coincodecap.com/crypto-trading-bots-in-the-us) | [变化回顾](https://coincodecap.com/changelly-review)
*   [在印度利用加密套利赚取被动收入](https://coincodecap.com/crypto-arbitrage-in-india)
*   [霍比评论](https://coincodecap.com/huobi-review) | [OKEx 保证金交易](https://coincodecap.com/okex-margin-trading) | [期货交易](https://coincodecap.com/futures-trading)
*   [面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [杠杆代币的终极指南](/coinmonks/leveraged-token-3f5257808b22)