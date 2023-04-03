# 我的闪电节点设置与 c-lightning

> 原文：<https://medium.com/coinmonks/my-lightning-node-setup-with-c-lightning-45bbb9993c0?source=collection_archive---------5----------------------->

## 随着 [Blockstream 宣布](https://blockstream.com/2018/06/25/c-lightning-06.html)c-Lightning 的发布，这可能是一个推出 mainnet Lightning 网络节点的好时机！

![](img/6ab651410b90ae86a8607c8266e7e39e.png)

先决条件:我已经有一个 Linux 操作系统，一个比特币全节点和 Tor 启动并运行。

## 隐藏服务的设置

在大多数国家，运营 Tor 隐藏服务本身并不复杂，也不违法。此外，对于那些生活在压迫政权下的人来说，这可能是一个非常大的帮助。

首先我们打开`/etc/tor/torrc`文件并插入一些额外的行来创建版本 2 和版本 3 的隐藏服务:

```
HiddenServiceDir /var/lib/tor/lightning_service_v2/
HiddenServiceVersion 2
HiddenServicePort 9735 127.0.0.1:9735HiddenServiceDir /var/lib/tor/lightning_service_v3/
HiddenServiceVersion 3
HiddenServicePort 9735 127.0.0.1:9735
```

修改 torrc 后，Tor 服务需要重新启动才能生效:`$ systemctl restart tor@default`

Tor 现在生成两个新的隐藏服务。我们可以查看他们的洋葱地址:

```
$ sudo cat /var/lib/tor/lightning_service_v2/hostname 
yqygrtvwihl3exb2.onion
$ sudo cat /var/lib/tor/lightning_service_v3/hostname 
ihhsjdncang7ve2rvhuunvy45bxvmh2qwz3vh7mohbouizd36ywnxeid.onion
```

## 闪电守护程序配置

c-lightning 实现可以从 GitHub 下载[。`lightningd`守护进程有很多参数，可以从命令行或配置文件中配置。这是我的`~/.lightning/config`文件:](https://github.com/ElementsProject/lightning/releases)

需要一个空地址给`bind-addr`来[监听所有接口](https://github.com/ElementsProject/lightning/blob/master/doc/lightningd-config.5.txt)。因此该节点可同时用于输入 Tor 和 clearnet 连接。

`announce-addr` [可以多次使用](https://github.com/ElementsProject/lightning/blob/master/doc/lightningd-config.5.txt)来添加更多地址，这样守护程序可以通告 IPv4、Torv2 和 Torv3 地址。

对于每 kwu 4000 Satoshi 是一个非常低的值，但目前已经足够了。

127.0.0.1:9050 用于输出 Tor 连接。这样我们可以连接到其他隐藏的服务。

## 运行节点

## 提示和技巧

耐心点，这个技术和网络还不成熟！如果第一次不成功，再试一次。另一个。另一个。

多个 lightning 节点实例可以在同一台机器上运行。如果没有其他帮助，您可以启动另一个实例来检查您的第一个设置是否可操作。

由于 Tor 地址和 gossip 实现中的一些问题，一些浏览器站点看不到我的节点信息。探险者没有别名，没有肤色，没有 URI。为了解决这个问题，我只用我的 IPv4 地址启动了我的节点，然后等了几分钟，看看它是否会出现在 explorers 上。之后，我用 Tor 和 IPv4 地址重启了它。

见什么说什么:如果你遇到任何困难，考虑在 GitHub 上[开一个问题](https://github.com/ElementsProject/lightning/issues)。如果你设法解决了某个问题，在公开的地方写下来。下一个面临同样问题的人会发现它，并会为此而感激！

备份您的`hsm_secret`文件！它是钱包私钥的种子！

这里是我的闪电节点 URI，加入我吧！

```
03d67f36c4f81789e2fe425028bacc96b199813eae426c517f589a45f1136c1fe5@89.133.16.197:973503d67f36c4f81789e2fe425028bacc96b199813eae426c517f589a45f1136c1fe5@yqygrtvwihl3exb2.onion:973503d67f36c4f81789e2fe425028bacc96b199813eae426c517f589a45f1136c1fe5@ihhsjdncang7ve2rvhuunvy45bxvmh2qwz3vh7mohbouizd36ywnxeid.onion:9735
```

## 资源

感谢那些走在我前面的人！

[](/@dougvk/run-your-own-mainnet-lightning-node-2d2eab628a8b) [## 运行您自己的 mainnet Lightning 节点

### 使用即时闪电网络从无到有

medium.com](/@dougvk/run-your-own-mainnet-lightning-node-2d2eab628a8b) [](/@meeDamian/c-lightning-node-on-rbp3-b950660fb835) [## RBP3 上的闪电网络节点(c-lightning)

### 这是指南的第三部分，帮助你在已经运行的比特币节点之外建立一个 c-lightning 节点…

medium.com](/@meeDamian/c-lightning-node-on-rbp3-b950660fb835)