# 如何用 Docker 在 20 分钟内开始挖矿？

> 原文：<https://medium.com/coinmonks/how-to-start-mining-with-docker-in-20-minutes-4b338c5551c8?source=collection_archive---------1----------------------->

实际上，Docker 使用容器和不可变映像来构建和打包软件及其所有依赖项。

更多细节你可以在[http://www.docker.com](http://www.docker.com)上看到

在这里，我将向您详细解释如何在 Linux 主机服务器上准备和启动挖掘——在这里的示例中，我将在 AWS EC2 实例上使用 Linux Ubuntu 16.04 LTS 服务器。

它真的以一种非常简单的方式工作；您需要将您的 CPU/GPU 连接到任何矿池；我们将使用 NiceHash 挖掘池

在这里登记..[http://www.cinehash.com](http://www.cinehash.com)

你需要按照步骤来

## **1-安装 AWS EC2 Ubuntu 16.0.4 LTS**

## **2-安装对接设备**

`**$ curl -sL https://get.docker.com | sh**`

## **3-初始化蜂群**

`**$ docker swarm init**`

## **4-通过挖掘暗夜开始挖掘**

`$ docker service create --mode=global \
--name miner alexellis2/cpu-opt:2018-1-2 ./cpuminer \
-a cryptonight \
-o stratum+tcp://cryptonight.usa.nicehash.com:3355 \
-u` 38 hadmjvuffwcwxrwfvqrdtbutro 1 e 43 gt`.cloud1`

**注意:这里您需要将“美国”替换为您的位置，例如“in”**

还有你的钱包 ID "[38 hadmjvufwcwxrwfvqrdtbutro 1 e 43 gt](https://www.nicehash.com/miner/38HADMJVufWcwXRWfVQRRdTbUTRo1E43Gt)"

## 5-停止/暂停采矿机

$ docker 服务规模挖掘器=0

## 6-恢复矿工

$ docker 服务规模挖掘器=1

## 7-查看余额

`[https://www.nicehash.com/miner/](https://www.nicehash.com/miner/38HADMJVufWcwXRWfVQRRdTbUTRo1E43Gt)`[38 hadmjvufwcwxrwfvqrdtbutro 1 e 43 gt](https://www.nicehash.com/miner/38HADMJVufWcwXRWfVQRRdTbUTRo1E43Gt)

同样，这里需要用您自己的 wallet id "[38 hadmjvuffwcwxrwfvqrdtbutro 1 e 43 gt](https://www.nicehash.com/miner/38HADMJVufWcwXRWfVQRRdTbUTRo1E43Gt)"替换

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)