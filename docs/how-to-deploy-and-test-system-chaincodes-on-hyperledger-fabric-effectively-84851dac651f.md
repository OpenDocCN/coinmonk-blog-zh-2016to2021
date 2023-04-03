# 如何在 Hyperledger Fabric 上有效地部署和测试系统链码

> 原文：<https://medium.com/coinmonks/how-to-deploy-and-test-system-chaincodes-on-hyperledger-fabric-effectively-84851dac651f?source=collection_archive---------0----------------------->

![](img/8e0c5e6bdfdb63813edac9c683b03d43.png)

我一直在从事一个项目，该项目要求我在 [Hyperlerledger Fabric](https://hyperledger-fabric.readthedocs.io/en/release-1.4/) 上实现自定义系统链码( **SCC** ),我在 2019 年 1 月的大部分时间都在从事这个项目，并试图了解 Fabric 如何工作，以及测试和部署系统链码的最佳方式是什么。在这个过程中，我与 hyperledger 社区的一些成员进行了互动，他们在解释不明显的做法方面非常有帮助。在这篇文章中，我将尝试解释在测试时有效部署系统链代码的最佳步骤和实践。本教程将不详细讨论编写系统链代码，因为它们在文档中有详细介绍。

## 为什么使用系统链码？

如果你刚开始看面料，问这个问题，在大多数情况下，常规链码就足够了。但是，如果您需要在对等体中直接完成某些事情，而不是在受限的 docker 容器中，那么您将需要使用系统链代码。这方面的一个例子是拥有一个独立的通信媒介(即对等体之间的直接套接字通信)而不是使用通道。

## 怎么写系统链码？

编写实际的系统链码与普通的链码没有太大的不同，有一个“*垫片。Chaincode* "接口定义了一些需要由 Chaincode 实现的方法。我不会在此赘述，hyperledger [文档](https://hyperledger-fabric.readthedocs.io/en/release-1.3/chaincode4ade.html)对此做了很好的解释。

对于像对等 ip 查找系统链码这样的东西，下面的代码就可以了

```
package mainimport (
 “fmt”“github.com/hyperledger/fabric/core/chaincode/shim”
 pb “github.com/hyperledger/fabric/protos/peer”
)// New returns an implementation of the chaincode interface.
func New() shim.Chaincode {
 fmt.Println(“Hello”) peer1org1, err := net.LookupHost("peer1.org1.example.com")
 if err != nil {
    log.Fatalf("net.LookupHost peer1.org1.example.com failed with %s\n", err)
  }
  fmt.Println(peer1org1) 
  return &helloworldscc{}
}type helloworldscc struct{}// Init implements the chaincode shim interface
func (s *helloworldscc) Init(stub shim.ChaincodeStubInterface) pb.Response {
 return shim.Success(nil)
}// Invoke implements the chaincode shim interface
func (s *helloworldscc) Invoke(stub shim.ChaincodeStubInterface) pb.Response {
 return shim.Success(nil)
}
```

## 部署 SCC

部署 SCC 有两种选择。你可以用标准格式来部署它，就像查询系统 chaincode 和其他内置系统是如何部署的一样，即它们是静态链接的。选项是使用 Go 插件，并将 SCC 动态链接到对等体。

第一种方法的问题是需要为系统链代码中的每次更改重建 docker 映像。这需要 6-10 分钟。

在第二种方法中，只需要构建 Go 插件并提供链接配置。我将介绍这种方法所需的步骤，以及如何运行端到端测试。

## 构建 SCC 插件

系统链码插件可以用下面的标志来构建。

```
go build -buildmode=plugin
```

为了运行端到端测试，我们需要将其构建在与结构对等 docker 映像相同的平台中。以下命令将插件挂载到 fabric-baseimage 的 docker 容器中，并在那里构建插件。

```
docker run -i --rm  -v PATH_TO_PLUGIN:/opt/gopath/src/github.com/PLUGIN_NAME -w /opt/gopath/src/github.com/PLUGIN_NAME \
-v PATH_TO_FABRIC:/opt/gopath/src/github.com/hyperledger/fabric \
hyperledger/fabric-baseimage:amd64-0.4.14 \
go build -buildmode=plugin -tags
```

## 设置 Fabric 以允许 SCC 插件的动态链接:

我们需要添加关于插件的信息，并在 core.yaml 文件中启用它。

```
system:
         cscc: enable
         lscc: enable
         escc: enable
         vscc: enable
         qscc: enable
         helloworldscc: enable

 systemPlugins:
       - enabled: true
         name: helloworldscc
         path: /opt/lib/helloworld.so
         invokableExternal: true
         invokableCC2CC: true
```

在 fabric 1.4 版本中，SCC 插件被禁用，但他们添加了一个秘密标志以允许更快的开发。这个问题的 jira 有更多关于为什么他们禁用它的信息。

对等体必须使用以下标志来构建

```
GO_TAGS+=" pluginsenabled" make peer
```

端到端测试使用 docker 容器，因此容器也应该使用

```
DOCKER_DYNAMIC_LINK=true GO_TAGS+=" pluginsenabled" make peer-docker
```

## 运行端到端测试:

我喜欢使用第一个出现在 [fabric-samples](https://github.com/hyperledger/fabric-samples) 仓库中的网络环境。确保修改对等体的 docker 映像，以挂载一个指向先前 core.yaml 条目中提供的路径的卷。

## 关于我:

我是 UIUC 的计算机科学硕士研究生。我在由安德鲁·米勒教授领导的分散系统实验室工作

## 免责声明:

我不是 hyperledger fabric 的开发人员，我的知识和观点仅限于我的用例。

[![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)

[**Click to Read today’s top story**](http://bit.ly/2G71Sp7)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)