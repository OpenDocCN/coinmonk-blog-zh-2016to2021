# 在服务器上设置 IPFS 节点

> 原文：<https://medium.com/coinmonks/set-up-ipfs-node-on-the-server-20e111b434d6?source=collection_archive---------2----------------------->

![](img/e1dbb52e9ce1327c54dcfad562b09810.png)

IPFS(星际文件系统)是建立对等网络的协议，基于资源的内容而不是像 HTTP 中那样基于物理位置来寻址资源。IPFS 给了我们一些区块链的保证，比如分散化和不可更改的存储，而价格只是你必须支付的交易费的一小部分。参与 IPFS 网络是免费的。

了解 IPFS 的关键是，一旦你添加了文件，网络就不会存储它们。将文件添加到 IPFS 不会将它们上传到任何地方，而只是意味着您将它们添加到您在节点上托管的本地存储库中。除非其他对等方有兴趣在他们的节点上托管您的内容，否则一旦您关闭您的节点，您添加的文件将无法为其他人所用，直到您重新联机。缓存机制缓解了这个问题，因为对获取内容感兴趣的对等点会将内容保存在缓存中。积极的垃圾收集器会快速移除未使用的文件。您不应该依赖缓存来保持文件在线。

为了可靠地与其他对等方共享文件并使用 IPFS 来托管网页等，您需要设置一台服务器，而不依赖于您的个人设备连接。

# 安装和设置 IPFS

我将使用 AWS EC2 使用默认的 VPC 在 Amazon Linux 2 上启动我的服务器。t2.micro 实例在免费层不会花费您一分钱，对于 IPFS 节点和一些 web 服务来说已经足够好了。

安装戈朗和 IPFS。

```
$ yum update -y
$ yum install -y golang

$ wget https://dist.ipfs.io/go-ipfs/v0.4.15/go-ipfs_v0.4.15_linux-amd64.tar.gz$ tar -xvf go-ipfs_v0.4.15_linux-amd64.tar.gz
./go-ipfs/install
```

如果安装失败，您可以手动将可执行文件移动到您的 bin 路径。

```
$ mv ./go-ipfs/ipfs /usr/local/bin
```

初始化本地 IPFS 配置并添加您的第一个文件。

```
> ipfs init
echo "<h1>Michal</h1>" > index.html> ipfs add index.html
added Qma1PYYMwbgR3GBscmBV7Zx8YgWdhBUAY6z27TznhrBet9 index.html> ipfs cat Qma1PYYMwbgR3GBscmBV7Zx8YgWdhBUAY6z27TznhrBet9
<h1>Michal</h1>
```

恭喜你！您刚刚向 IPFS 存储库中添加了一个文件。虽然您可以获取它，但它只能在本地工作。要加入网络，您应该运行 IPFS 守护进程。

如果你的防火墙没有阻止连接，那么你应该能够从远程节点获取你的文件或者使用像`[https://ipfs.io/ipfs/](https://ipfs.io/ipfs/.)` [这样的公共网关。](https://ipfs.io/ipfs/.)

# 启动时运行 IPFS 守护程序

最好将 IPFS 守护进程作为服务启动，而不是终端附加进程。让我们定义一个负责运行 IPFS 守护进程服务的简单单元文件。

```
$ vi /etc/systemd/system/ipfs.service
```

复制和粘贴单位文件定义。

```
[Unit]
Description=IPFS Daemon
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=simple
ExecStart=/usr/local/bin/ipfs daemon --enable-namesys-pubsub
User=ec2-user

[Install]
WantedBy=multi-user.target
```

使用`--enable-namesys-pubsub`运行守护进程受益于几乎即时的 IPNS 更新。IPNS 是 IPFS 命名系统，允许可变的网址。编辑单元文件后，重新加载守护程序，使服务能够在引导时启动，并启动服务。

```
sudo systemctl daemon-reload
sudo systemctl enable ipfs
sudo systemctl start ipfs
```

现在，您可以重新启动您的实例，并确保 IPFS 是否已恢复运行。

```
sudo systemctl status ipfs
```

# 使网关可公开访问

如果您愿意，可以公开访问您的 IPFS 网关。更改网关配置以侦听所有可用的 IP 地址。

在`~/.ipfs/config`改变

```
"Gateway": "/ip4/127.0.0.1/tcp/8080"
```

到

```
"Gateway": "/ip4/0.0.0.0/tcp/8080"
```

# 结论

我在 EC2 上运行 IPFS 节点已经有一段时间了，我没有遇到任何大的问题。您可以使用`scp`通过 ssh 将文件复制到您的远程服务器。对于编程访问，您可以使您的 IPFS 网关可写，并使用 [IPFS HTTP API](https://ipfs.io/docs/api/) 。IPFS 有很多有创意的用例！

*原载于 2018 年 6 月 17 日*[*michalzalecki.com*](https://michalzalecki.com/set-up-ipfs-node-on-the-server/)*。*