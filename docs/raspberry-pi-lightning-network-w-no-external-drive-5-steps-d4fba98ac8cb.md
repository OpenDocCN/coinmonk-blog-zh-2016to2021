# 覆盆子π +闪电⚡+ Docker🐋&无外部驱动器(6 步)。

> 原文：<https://medium.com/coinmonks/raspberry-pi-lightning-network-w-no-external-drive-5-steps-d4fba98ac8cb?source=collection_archive---------2----------------------->

这是一个 6 步教程，让树莓派运行一个功能齐全的闪电节点。

![](img/663d7c841e77347dfe161fd36ab8f289.png)

+ Docker

诀窍是使用 Docker +比特币和修剪过的数据，让一切运行在 16 GB 以下。

# 步骤 0。所需设备

1.  有大约 300 GB 可用空间的辅助计算机。
2.  带有互联网连接的树莓 Pi3 (Pi)。(参见其他教程)

# 第一步。下载完整的比特币区块链。

我们需要纯模式的比特币区块链。我们将在步骤 3 中将其复制到 Pi。

1.  安装 Bitcoind (Linux)或 Mac
2.  开始同步比特币区块链。
3.  等待进度= 1.00。

```
sudo apt-get install bitcoind # (For Linux)
brew install bitcoind # (For Mac)
bitcoind -prune=555
... wait ... you can do steps 2 and 3.
```

*注意**区块链数据存储在

*   /库/应用支持/比特币(Mac)
*   ~/.比特币/ (Linux)

# 第二步。在你的 Pi 上安装 Docker。

我们使用 Docker 来容器化 LND 和 Bitcoind 后台服务。

1.  安装 Docker..
2.  将当前用户(pi)添加到 docker 用户组中。
3.  确认安装。

```
curl -ssl [https://get.docker.com](https://get.docker.com) | sh
sudo usermod -a -G docker $USER
docker --version
```

# 第三步。在您的 Pi 上安装 Docker-Compose

Docker-compose 用于构建和连接我们的后端。

1.  安装 python-pip。
2.  Pip 安装 docker-compose。
3.  将当前用户(pi)添加到 docker-compose 用户组
4.  确认安装。

```
sudo apt-get -y install python-pip
sudo pip install docker-compose
sudo usermod -a -G docker-compose $USER
docker-compose --version
```

# 第四步。下载这个存储库并复制修剪过的比特币数据

一个 16 BG 的新 PI 无法容纳整个未修剪的比特币区块链(约 300 GB)，因此我们将从我们的辅助计算机复制修剪后的数据(步骤 1)

1.  从 [DockerBitcoindLN](https://github.com/unconst/DockerBitcoindLND.git) D 中克隆 docker 文件
2.  将区块链数据从辅助计算机复制到 Pi 上。

```
git clone [https://github.com/unconst/DockerBitcoindLND.git](https://github.com/unconst/DockerBitcoindLND.git) && cd DockerBitcoinLNDscp -r <ip_of_secondary>:path/to/bitcoin_dir bitcoind/bitcoin_data# e.g scp -r 192.168.0.12:~/.bitcoin bitcoind/bitcoin_data
```

# 第五步。编写后端。

1.  组合和构建容器。
2.  别名 lncli。
3.  别名 bitcoind cli。

```
sudo docker-compose up --build
sudo alias lndcli='docker exec -i -t lnd_container lncli'
sudo alias bitcoin-cli='docker exec -i -t bitcoind_container bitcoin-cli'
```

# 第六步。等待节点同步。

在此步骤之后，您需要等待容器完成同步。通过日志检查这个进度。

```
docker logs bitcoind_container
docker logs lnd_container
```

您可以使用 CLIs 与 docker 容器进行交互，就像它们在本地运行一样。

祝你好运。希望这有用。

😉

:wq

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)[![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)

[**Click to read today’s top story**](http://bit.ly/2G71Sp7)