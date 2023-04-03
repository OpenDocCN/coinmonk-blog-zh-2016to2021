# 我如何主持一个关于 IPFS 的网站

> 原文：<https://medium.com/coinmonks/how-i-hosted-my-website-on-ipfs-431919d7440a?source=collection_archive---------1----------------------->

![](img/01a20592da51ef0c15cfc65a2730354e.png)

所以，在过去的几周里，我一直在写我的论文，我有机会深入研究了[行星间文件系统](https://ipfs.io/)协议。我相信你们大多数人都知道它，但对于那些不知道的人来说，IPFS 是一个对等超媒体协议，旨在取代传统的 HTTP，使 web 更快、更安全、更开放。它是去中心化的，使用类似于 BitTorrent 的点对点协议和 Git 的版本系统。(我不会在这里讲述 IPFS 的内部运作，也许我会在另一篇文章中讲述)。然而，在完成我的论文后，我想进一步探索 IPFS，并为自己建了一个静态的作品集网站，上传到 IPFS，瞧！免费分散托管。

> [发现并回顾最佳区块链开发工具](https://coincodecap.com)

你可以查看我在 IPFS 主持的网站[这里](https://ipfs.infura.io/ipfs/QmUbw27P1w1Q8Rejr9YB4aKCkHnDq1CxyVhsXvywoudoSq/)

现在，我知道你在想什么:
——*这是什么样的魔法？* —
不要害怕，下面我将带你完成在 IPFS 托管你的网站所需的步骤。

![](img/0574da58d62b886bb43962f0ad84ea27.png)

## 步骤 1:安装 IPFS

为您的平台下载 IPFS，并按照[官方 IPFS 文档](https://ipfs.io/docs/install/)
上的说明进行安装。我使用 Ubuntu Linux，所以下载后，我将解压并使用以下命令进行安装:

```
$ tar xvfz go-ipfs.tar.gz
$ cd go-ipfs
$ ./install.sh
```

![](img/8c22ff087f194934af0437ab5ddf54c8.png)

## 第二步:准备你的文件

我会假设你有你想要上传到 IPFS 的文件。
如果你没有，不要担心。下面是你可以使用的快速`html`和`css`例子，这是我从 w3schools.com 抄来的。没什么特别的。

```
<!DOCTYPE html>
<link rel="stylesheet" type="text/css" href="styles.css">
<html>
    <body>
        <h1>My First Heading</h1>
        <p>My first paragraph.</p>
    </body>
</html>
```

```
body {
    background-color: #ccc;
}
```

将这些保存为`index.html`文件和`style.css`

![](img/1a673787294e1016b594a3d19b6d1c89.png)

## 第三步:开始 IPFS

您可以使用以下命令轻松启动 IPFS 节点:

```
$ ipfs daemon
```

![](img/d5c82095831ddcd23aef8b9af771273c.png)

## 第四步:上传到 IPFS

我的文件储存在桌面上一个叫做`test-ipfs`的文件夹里。
从终端运行以下命令会将整个文件夹添加到 IPFS，并为每个文件生成一个地址:

```
$ ipfs add -r test-ipfs/
```

输出:

```
added QmX6Q9HfSNwDZuXvQRMNewmPUGp8uS5Ef6dPbF2AYw792M test-ipfs/index.html
added QmXBuwyFQvGy5Zq5MUW5FxcFfxw1YFnbsPuHLYpZUZ82Rd test-ipfs/styles.css
added QmaEBknbGT4bTQiQoe2VNgBJbRfygQGktnaW5TbuKixjYL test-ipfs
```

最后一行生成的散列是你的站点的根。打开
`https://gateway.ipfs.io/ipfs/<your hash here>`即可访问。所以示例站点可以在这里找到:
`[https://gateway.ipfs.io/ipfs/QmaEBknbGT4bTQiQoe2VNgBJbRfygQGktnaW5TbuKixjYL](https://gateway.ipfs.io/ipfs/QmaEBknbGT4bTQiQoe2VNgBJbRfygQGktnaW5TbuKixjYL)`

![](img/34b7c669442ae12028f211f0f7fa0757.png)

## 第五步:通过网关访问你的网站

只要您的网站被其他对等方访问，IPFS 就会托管您的网站，因为它在被访问时会传播到其他节点。考虑到这一点，有几种方法可以确保您的网站随时可以访问，如下所示:

*   运行您自己的节点并将其托管在那里(并为 IPFS 网络做出贡献)。
*   使用 IPFS 网关托管您的网站。

就我个人而言，我使用了后者，并选择了 Infura.io 来永久托管我的网站。你可以简单地通过他们的网关访问为你的内容生成的哈希来做同样的事情，如下:
[https://ipfs . in fura . io/ipfs/qmaebknbgt 4 btqiqo 2e vngbjbrfygqgktnaw 5 tbukixjyl/](https://ipfs.infura.io/ipfs/QmaEBknbGT4bTQiQoe2VNgBJbRfygQGktnaW5TbuKixjYL/)
注意我们在链接中只把`gateway.ipfs.io`改成了`ipfs.infura.io`。这将通过 infura 节点访问所请求的内容，这样做将永久创建文件的副本。

![](img/b3f07e7825f33cbf410369adefb13937.png)

# 未来实施

使用 infura 在 IPFS 上托管你的内容是一个很好的开始。然而，整个 IPFS 方法有几个问题:

*   网址很丑，没人会记得。我们需要一种方法使它像 something.com 或 anything.org 一样漂亮。
*   任何时候你对你的网站做了更改并再次上传到 IPFS，你的内容的散列将会改变，这意味着网站的地址也会改变。

目前，这些问题没有简单的解决方法。重要的是要记住，IPFS 技术还处于早期阶段，还有发展的空间。在这篇由 Mark Pors 撰写的关于 Hackernoon 的文章[中，我们通过不同的方式来探讨这些问题。](https://hackernoon.com/ten-terrible-attempts-to-make-the-inter-planetary-file-system-human-friendly-e4e95df0c6fa)

![](img/07bbb706e465f78553d3d35faa4fe64e.png)

我希望你喜欢这篇文章，并发现它很有用，如果你发现了上述任何问题的解决方法，请在下面的评论中发表！

## [跟我来](https://steemit.com/@cryptoctave)

媒体由[先生 div](http://mrdiv.tumblr.com/)

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [电网交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [有哪些交易信号？](https://coincodecap.com/trading-signal) | [比特斯坦普 vs 比特币基地](https://coincodecap.com/bitstamp-coinbase)
*   [ProfitFarmers 回顾](https://coincodecap.com/profitfarmers-review) | [如何使用 Cornix Trading Bot](https://coincodecap.com/cornix-trading-bot)
*   [如何在势不可挡的域名上购买域名？](https://coincodecap.com/buy-domain-on-unstoppable-domains)
*   [印度的秘密税](https://coincodecap.com/crypto-tax-india) | [altFINS 审查](https://coincodecap.com/altfins-review) | [Prokey 审查](/coinmonks/prokey-review-26611173c13c)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   开发人员的最佳加密 API
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [杠杆代币](/coinmonks/leveraged-token-3f5257808b22)终极指南