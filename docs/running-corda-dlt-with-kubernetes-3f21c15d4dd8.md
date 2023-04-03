# 和库伯内特一起经营科达·DLT

> 原文：<https://medium.com/coinmonks/running-corda-dlt-with-kubernetes-3f21c15d4dd8?source=collection_archive---------5----------------------->

![](img/04bfa0c95322803d7bce366a5d1d2519.png)

[source](https://www.corda.net/)

在[明天实验室](https://tomorrow.fi)，我们正在与一群芬兰银行建立一个数字房地产交易平台。我们的目标不是将其构建为传统的集中式应用，而是利用区块链技术构建一个更加分散的系统。我们正在使用的平台是一个名为 [Corda](https://github.com/corda/corda) 的分布式账本(DLT)产品。这是一个关于如何使用运行在 [Kubernetes](https://kubernetes.io) 集群中的 Docker 容器建立 Corda 测试网络的教程。

我们使用最新的 Corda 开源版本 3.1 和 H2 数据库。我们的 Docker 映像管道有两个阶段:第一步是构建一个没有任何 CorDapp 的基础 Corda 映像，第二步是构建一个包含 CorDapp jars 的最终映像。基本 docker 文件如下所示:

然后，该图像被标记为 tag tomorrow/corda:latest。为了运行 Corda，我们使用一个 shell 脚本在 tmux 会话中启动 Corda 节点。这样，如果以后需要的话，很容易将它附加到 Corda Shell 中。run-corda.sh 如下所示:

下一步是构建 CorDapps jar。为此，我们使用 gradle 并将 jar 复制到文件夹 build/cordapps。然后下一步是用下面的 Dockerfile 构建最终的图像。

太好了！现在我们有了一个可以在 Kubernetes 中运行的 Docker 映像。为了运行 Corda 测试网络，我们必须为所有节点创建配置，然后生成证书和一些其他必要的文件。更多详情，请参见此处的说明。在我们的例子中，我们需要三个配置文件:公证人、甲方和乙方。这是一个最小的 Corda 设置，由两方和一个节点作为非验证公证人。配置存储在名为 config 的文件夹中，如下所示:

然后，我们运行 Corda 网络引导工具，为网络生成必要的文件。您可以从[这里](http://downloads.corda.net/network-bootstrapper-corda-3.0.jar)下载该工具，并使用以下命令运行它。

`java -jar network-bootstrapper-corda-3.0.jar config`

我们选择使用 Kubernetes secrets 来存储节点的配置、证书和其他必要文件。这是我们用于为甲方配置创建 Kubernetes 机密的命令:

```
kubectl create secret generic partya-config \
— from-file=./config/partya/node.conf \
— from-file=./config/partya/network-parameters \
— from-file=./config/log4j.xml
```

注意！我们还使用自己的 log4j 配置文件，以便可以配置日志记录。使用以下命令存储甲方的证书:

```
kubectl create secret generic partya-keystore \
--from-file=./config/partya/certificates/nodekeystore.jks \
--from-file=./config/partya/certificates/sslkeystore.jks \
--from-file=./config/partya/certificates/truststore.jks
```

为其他节点创建机密的命令类似。最后一个棘手的部分是将节点信息文件分发给所有节点。Corda 3.x 网络模型要求这些文件在所有节点上都可用。因此，我们创建了一个将在所有节点中使用的秘密。它是使用以下命令创建的:

```
kubectl create secret generic additional-node-infos \
--from-file=./config/notary/nodeInfo-9D193AC8883388E96664F3B0CC0AD3D162114F85F1633A69EF8CB30DA0993065 \  --from-file=./config/partya/nodeInfo-E4477B559304AADFC0638772C0956A38FA2E2A7A5EB0E65D0D83E5884831879A \  --from-file=./config/partyb/nodeInfo-B1F1FD9F15FCFDE1C9EE7A59504A44D669AB67B655597D60B691138E9C12E93C
```

那些看起来神秘的文件名是 Corda bootstrapper 根据您的节点配置生成的节点哈希。因此，请记住根据引导程序生成的内容来更改名称。现在，我们终于可以为 Kubernetes 配置部署了。以下是甲方节点的示例:

此配置中需要提及的几件事:

*   对于 p2p 网络，如果您打开 10002 端口就足够了，但是我们需要 RPC 端口 10003 用于外部访问(我们有一个单独的 REST API 用于使用 Corda)。
*   主机名对于正确配置非常重要
*   这不是高可用性配置。开源版本的 Corda 不支持。所以使用 Recreate 作为部署策略最有意义。它防止两个 pod 试图同时在同一地址运行。
*   该图像应更改为之前创建的图像。在我们的例子中，它是 dias/app:1.0.116。它还必须指向您的 Kubernetes 集群可以访问的 Docker 存储库。我们使用私有注册表，并使用这些[指令](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry)来配置访问。
*   内存限制可能有点高。我们还没有调整它们。

其他节点的配置看起来相似。在我们创建它们之后，最后一步是使用命令应用所有配置:

```
kubectl apply -f .
```

理论上，网络地图服务(在这种情况下是公证节点)应该在其他节点之前启动，以便它们可以在启动时连接到它。实际上，节点等待连接一段时间，对我们来说这已经足够了。一次启动它们会更简单。盈利前的最后一步是通过连接到任何节点来测试网络设置工作。它是这样工作的:

```
kubectl exec -it partya-5bddb8df-pqvjp — /bin/bash
tmux attach -t corda
run networkMapSnapshot
```

这应该显示连接到网络的所有节点。在这种情况下是三个节点。请记住使用 Ctrl+b 后跟 d 退出 tmux 会话。tmux 的警告是，如果您不想终止应用程序，就不应该终止会话。

设置有点长，也没那么简单。在某个时候，R3 会提供一个您可以连接到的网络。然而，目前 Kubernetes 是运行您自己的测试网络的好方法。初始设置后，很容易更新应用程序，它工作顺利。我们用 Google Kubernetes 引擎运行我们的测试网络，但是我们不使用任何 Google 特有的功能。这种设置应该适用于任何 Kubernetes 实现。

如果我有时间，为 Docker 和 Kubernetes 设置创建一个 Github repo 可能是个好主意。似乎有很多人在这个设置中挣扎。