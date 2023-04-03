# ERC20 令牌作为 Hyperledger 结构 Golang 链代码

> 原文：<https://medium.com/coinmonks/erc20-token-as-hyperledger-fabric-golang-chaincode-d09dfd16a339?source=collection_archive---------0----------------------->

![](img/f0e1237809517fceca5f9b6fc80268d1.png)

与以太坊区块链一样，Hyperledger Fabric platform (HLF)可用于令牌创建，实现为智能合约(HLF 术语中的链码),保存用户余额。与以太坊不同，HLF 链码不能使用用户地址作为持有者密钥，因此我们将使用成员服务提供商(MSP)标识符和用户证书标识符的组合。下面是如何在 Hyperledger Fabric 平台上使用 [CCKit](https://github.com/s7techlab/cckit) chaincode 库创建 Golang chaincode 令牌的简单示例。

# 什么是 ERC20 令牌标准

令牌标准是以太坊中令牌智能合约标准化的一种尝试，它描述了以太坊令牌合约必须实现的功能和事件。以太坊区块链上的大多数主要代币都符合 ERC20 标准。ERC-20 有很多好处，包括统一代币钱包，以及交易所只需提供代币合同地址就能列出更多代币的能力。

# ERC20 实施基础

本质上，以太币合同是一个智能合同，它持有帐户地址及其余额的映射。余额是由合同创建者定义的一个值，它可以是可替换的物理对象，也可以是另一个货币值。这种余额的单位通常称为代币。

ERC20 功能有:

*   `balanceOf`:返回所有者标识符的令牌余额(以太坊的情况下为账户地址)
*   `transfer`:向我们选择的所有者标识符转移一笔金额
*   `approve`:设置一个指定的所有者标识符可以代表我们消费的代币数量
*   `allowance`:检查一个所有者标识符可以代表我们花费多少钱
*   `transferFrom`:指定一个所有者标识符，如果那个所有者标识符允许我们花费一些代币，我们就从那个所有者标识符转账。

# Hyperledger 结构中的所有者标识符

在 Hyperledger 结构网络中，所有参与者都有一个为其他参与者所知的身份。默认的[成员服务提供者](https://hyperledger-fabric.readthedocs.io/en/release-1.3/msp.html)实现使用 X.509 证书作为身份，采用传统的公钥基础设施(PKI)分层模型。

使用关于提议的创建者和资产所有权的信息，链代码应该能够实现链代码级的访问控制机制，检查参与者是否可以启动更新资产的事务。相应的链码逻辑必须能够存储与资产相关的“所有权”信息，并根据提议对其进行评估。

作为 HLF 网络中的唯一所有者标识符(令牌余额持有者),我们可以使用 MSP 标识符和用户身份标识符的组合。身份标识符—是 X.509 证书的`Subject`和`Issuer`部分的串联。这个 ID 保证在 MSP 中是唯一的。

C [客户端身份链代码库](https://github.com/hyperledger/fabric/tree/master/core/chaincode/lib/cid)允许编写基于客户端身份(即链代码的调用方)做出访问控制决定的链代码。

特别是，您可以根据与客户端相关的以下一项或两项做出访问控制决策:

*   客户端身份的 MSP(成员资格服务提供商)ID
*   与客户端身份相关联的属性

CCkit 包含 [identity](https://github.com/s7techlab/cckit/tree/master/identity) 包，其结构和功能可用于在链码中实现访问控制。

# 示例入门

在我们的示例中，我们使用 CCKit 路由器来管理智能合同功能。在你开始之前，一定要拿到`CCkit`:

`git clone git@github.com:s7techlab/cckit.git`

并使用`dep`命令获得依赖关系:

`dep ensure -vendor-only`

ERC20 示例位于 [examples/erc20](https://github.com/s7techlab/cckit/tree/master/examples/erc20) 目录下。

# 定义令牌智能合约功能

首先，我们需要定义链码函数。在我们的例子中，我们使用来自 [CCKit](https://github.com/s7techlab/cckit/) 的 [router](https://github.com/s7techlab/cckit/tree/master/router) 包，它允许我们以一致的方式定义链码方法及其参数。关于链码方法路由、中间件、链码调用上下文的细节在之前的文章中有所描述。

首先，我们用参数`symbol`、`name`和`totalSupply`定义`init`函数(智能契约构造函数)。之后，我们定义了链码方法，实现了 ERC20 接口，用于 HLF 所有者标识符(MSP Id 和证书 Id 对)。从链码状态查询的方法以`query`为前缀，写入链码状态的方法以`invoke`为前缀。

因此，我们使用[默认链码](https://github.com/s7techlab/cckit/blob/master/router/chaincode.go)结构，将`Init`和`Invoke`处理委托给路由器。

# 链码初始化(构造函数)

Chaincode `init`函数(令牌构造函数)执行以下操作:

*   使用来自 [CCKit](https://github.com/s7techlab/cckit/) 的[所有者](https://github.com/s7techlab/cckit/tree/master/extensions/owner)扩展，将关于链码所有者的状态信息放入链码
*   将链码状态令牌配置-令牌符号、名称和总供应量
*   设置总供应的链码所有者余额

# 定义事件结构类型

我们使用[标识](https://github.com/s7techlab/cckit/tree/master/identity)包中的 [Id](https://github.com/s7techlab/cckit/blob/master/identity/entry.go) 结构，为`Transfer`和`Approve`事件定义结构:

# 实现令牌智能合约功能

查询函数非常简单——它只是从链码状态中读取值:

一些变化的状态函数更复杂。例如，在函数`invokeTransfer`中，我们:

*   接收函数调用方证书(通过 tx `GetCreator()`函数)
*   检查转移目的地
*   获取当前发票人(付款人)余额
*   检查余额以转移代币`amount`
*   获取收款人余额
*   在链码状态下更新付款人和收款人余额

# 测试

此外，我们可以通过 [CCKit MockStub 快速测试我们的链代码。](https://github.com/s7techlab/cckit/tree/master/testing)

为了开始测试，我们通过带有测试参数的 MockStub 初始化链代码:

在我们可以检查所有令牌操作之后:

完整示例可在此处找到[https://github . com/s7techlab/cc kit/tree/master/examples/ERC 20](https://github.com/s7techlab/cckit/tree/master/examples/erc20)

关于链码单元测试和测试驱动开发的更多信息，你可以在这里阅读:

> [https://medium . com/coin monks/test-driven-hyperledger-fabric-golang-chain code-development-dbec 4c b 78049](/coinmonks/test-driven-hyperledger-fabric-golang-chaincode-development-dbec4cb78049)

Chaincode 状态模式可以通过 protobuf 代码生成来实现，它允许定义一次数据模型，然后轻松地从各种数据源写入和读取结构化数据。

> [https://medium . com/coin monks/hyperledger-fabric-smart-contract-data-model-proto buf-to-chain code-state-mapping-191 CDC fa 0 b 78](/coinmonks/hyperledger-fabric-smart-contract-data-model-protobuf-to-chaincode-state-mapping-191cdcfa0b78)