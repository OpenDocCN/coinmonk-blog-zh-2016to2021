# 在 Fedora 上安装 Hyperledger Fabric V1.3 版

> 原文：<https://medium.com/coinmonks/installing-hyperledger-fabric-v1-3-on-fedora-c490f32092fb?source=collection_archive---------5----------------------->

![](img/be44c6845f66a1c1e7fd082c827feec0.png)

在宣布 IBM 将收购红帽 Linux 之后。我怀疑在未来超帐结构将在红帽系统之下。由于我对任何 Linux 发行版都没有好感，所以在升级我的 SSD 时，我立即切换到了 Fedora。

以下是我让 Hyperledger 进行全新安装的所有命令:

将 Docker 稳定报告添加到包管理器中

```
$sudo dnf config-manager \
    --add-repo \
    [https://download.docker.com/linux/fedora/docker-ce.repo](https://download.docker.com/linux/fedora/docker-ce.repo)
```

安装最新的 docker 版本

```
$ sudo dnf install docker-ce
```

安装 docker 撰写

```
sudo pip install docker-compose
```

下载并安装 GO

```
$ wget [https://dl.google.com/go/go1.11.1.linux-amd64.tar.gz](https://dl.google.com/go/go1.11.1.linux-amd64.tar.gz)
$ sudo tar -C /usr/local -xzf go1.11.1.linux-amd64.tar.gz
$ export PATH=$PATH:/usr/local/go/bin
$ export GOPATH=/usr/local/go
```

下载并安装节点

```
$ sudo su
# curl -sL https://rpm.nodesource.com/setup_8.x | bash -
$ su [YOUR USERNAME] 
$ sudo dnfinstall -y nodejs
$ sudo dnf install gcc-c++ make
```

创建新目录以安装 hyperledger 并将当前用户添加到 docker 组。

```
$ mkdir hyperledger
$ cd hyperledger
$ sudo usermod -aG docker $USER
$ newgrp docker
$ sudo curl -sSL [http://bit.ly/2ysbOFE](http://bit.ly/2ysbOFE) | bash -s 1.3.0
$ export PATH=$HOME/hyperlegder/bin:$PATH
```

运行第一个网络

```
$ cd fabric-samples/first-network
$ ./byfn.sh generate
$ ./byfn.sh up
```

如果您看到:

```
===================== Query successful on peer1.org2 on channel ‘mychannel’ ============================== All GOOD, BYFN execution completed ===========_____ _ _ ____ 
| ____| | \ | | | _ \ 
| _| | \| | | | | | 
| |___ | |\ | | |_| | 
|_____| |_| \_| |____/
```

你完了。恭喜你！

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)