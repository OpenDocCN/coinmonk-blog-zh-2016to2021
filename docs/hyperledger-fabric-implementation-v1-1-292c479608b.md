# Hyperledger 结构实施—第 2 部分

> 原文：<https://medium.com/coinmonks/hyperledger-fabric-implementation-v1-1-292c479608b?source=collection_archive---------7----------------------->

## Hyperledger Fabric 实现:使用 Fabric v1.1 SDK 时的重要信息

![](img/3db365a0ece50de3776d8bc4e16f9a01.png)

[source](https://wiki.hyperledger.org/projects/fabric)

在 [第 1 部分](/@pradeep.padmarajaiah/hyperledger-fabric-implementation-v1-1-part-1-chaincode-for-node-7b72aec2430d)中，我们探讨了使用 Node.js 和 Golang 的 Chaincode 及其 API 的概述。在这个故事中，我们将浏览关于 Fabric SDK 及其 API 的一般信息。

在后面的系列文章中，我们将探索使用 Chaincode 编写的端到端用例实现，以及它如何通过 Fabric SDK 与应用程序交互。最后一个系列将有一个前端应用程序，它将使用链码，智能合同和应用层与分类帐进行交互。

如果有人在设置开发环境方面需要帮助，请告诉我。我会在最后一个系列中添加它。

# **很高兴知道**

Fabric SDK 主要由两个组件组成。

> * **结构客户端**:整体网络交互和交易流 API
> ***结构 CA 客户端**:参与者管理 API

开发的代码流，可以大致分为三类

# **1)用户界面实现流程**

每个用户都必须有用户名、帐户名、用户关系、用户注册证书信息(注册对象)、用户成员资格服务提供商标识符
作为开始步骤，创建一个实现用户接口的 java 类。
**API 探索:**

> **接口**:org . hyperledger . fabric . SDK . enrollment，org . hyperledger . fabric . SDK . user

# 2)认证机构机制

*   创建客户端证书颁发机构的新实例。考虑本系列中的织物 CA。
*   注册并加载管理员用户。默认情况下，CA 将生成管理员用户加密
*   向现有管理员注册(API 使用该管理员作为注册员)并注册非管理员用户。
    **API 探索:**

> **类**:org . hyperledger . fabric _ ca . SDK . hfcaclient。
> 
> **方法** : createNewInstance()，setCryptoSuite()，enroll()，register()
> 
> **类**:org . hyperledger . fabric _ ca . SDK . registration request
> 
> **方法**:注册()

# 3)交易流程

*   创建结构客户端证书的新实例，并设置 CryptoSuite 和用户上下文。考虑到本系列中的管理工作是在完整的事务流上进行的。
*   创建或获取通道，并通过添加 peer、orderer、eventhub 对其进行初始化。

**API 探索:**

> **类**:org . hyperledger . fabric . SDK . HF client
> 
> **方法** : setCryptoSuite()，setUserContext()
> 
> **类**:org . hyperledger . fabric . SDK . channel
> 
> **方法** : newChannel()、addPeer()、addEventHub()、addOrderer()、initialize()、getChannel()

*   **调用查询**到总账是通过首先创建新的查询建议请求来实现的。然后为请求设置一个链码 id、链码函数和链码参数。通过在所需通道上调用 queryByChaincode 方法返回查询响应。
    **API 探索:，**

> **类**:org . hyperledger . fabric _ ca . SDK . HF client
> 
> **方法** : newQueryProposalRequest()
> 
> **类**:org . hyperledger . fabric . SDK . querybychaincoderequest
> 
> **方法** : setChaincodeID()，setFcn()，setArgs()
> 
> **类**:org . hyperledger . fabric . SDK . chain codeid
> 
> **方法** : newBuilder()，setName()，build()
> 
> **类**:org . hyperledger . fabric . SDK . proposal response
> 
> **方法**:getChaincodeActionResponsePayload()

*   **调用分类帐上的修改(创建/更新/删除)**请求通过首先创建新的交易建议请求来实现。然后为请求设置一个链码 id、链码函数和链码参数。通过在所需通道上调用 sendTransactionProposal 方法返回建议响应。如果建议有效，则使用 sendTransaction 通过返回事件的通道将交易发送给订购方。事件用于确定交易是否通过 isValid 方法提交至分类帐，并获取已提交交易的交易 id。
    **API 探索:**

> **类**:org . hyperledger . fabric _ ca . SDK . HF client
> 
> **方法**:newTransactionProposalRequest()
> 
> **类**:org . hyperledger . fabric . SDK . transactionproposalrequest
> 
> **方法** : setChaincodeID()，setFcn()，setArgs()
> 
> **类**:org . hyperledger . fabric . SDK . channel
> 
> **方法** : sendTransactionProposal()，sendTransaction()
> 
> **类**:org . hyperledger . fabric . SDK . chain codeid
> 
> **方法** : newBuilder()，setName()，build()
> 
> **类**:org . hyperledger . fabric . SDK . proposal response
> 
> **方法**:getChaincodeActionResponsePayload()

**订购服务**包含关于网络的关键信息，如

> *属于网络的组织详细信息
> *现有渠道详细信息。
> *组织和渠道关系
> *网络中任何变化的政策

添加一个**新的组织参与者**，

> *将新组织参与者的详细信息添加到订购者
> *如果组织希望维护一个分类帐，则需要添加对等详细信息(可选)

**同行**

> *默认情况下，所有对等方都是提交者。对等体还可以通过启用引导配置来承担签署者角色。
> *在指定端口上向相关方广播事件(当冻结被添加到分类帐时)

**身份**

> *每个操作都应该有数字签名。每个资源(订单节点、对等节点、用户)都必须拥有由证书颁发机构(CA)签名的证书。

**织物 CA**

> *为动态身份管理提供 REST APIs，包括注册、登记(获取证书)、撤销和重新登记。
> *这样创建的用户只是会员，不是管理员(管理频道&链码)

**参考文献**

**Fabric SDK Java**:[https://github.com/hyperledger/fabric-sdk-java](https://github.com/hyperledger/fabric-sdk-java)
**Fabric CA**:[http://hyperledger-Fabric-CA . readthedocs . io/en/latest/users-guide . html](http://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html)
**event hub**:[https://fabric-sdk-node.github.io/EventHub.html](https://fabric-sdk-node.github.io/EventHub.html)