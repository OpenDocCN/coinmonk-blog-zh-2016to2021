# Hyperledger 结构智能合同数据模型:protobuf 到 chaincode 状态映射

> 原文：<https://medium.com/coinmonks/hyperledger-fabric-smart-contract-data-model-protobuf-to-chaincode-state-mapping-191cdcfa0b78?source=collection_archive---------0----------------------->

## `**How specifying world state data model with protocol buffers can help in developing smart contracts**`

![](img/1083e7a412fee6880f4409d2fb9aacc0.png)

Chaincode 是一个与特定业务流程相关的特定领域程序。它以编程方式访问两个不同的分类帐——一个区块链，它不变地记录所有事务的历史；一个世界状态，它保存这些状态的当前值的缓存。智能合约开发人员的工作是获取可能控制金融价格或交付条件的现有业务流程，并用编程语言将其表达为智能合约。`Protobuf`(协议缓冲区的缩写)是语言中立、平台中立、可扩展的机制，用于序列化结构化数据。使用协议缓冲区有助于一次定义数据模型，然后轻松地从各种数据源读写结构化数据。

# 链码状态

分类帐的当前状态数据表示链交易日志中包含的所有关键字的最新值。因为当前状态表示通道已知的所有最新键值，所以它有时被称为世界状态。

链代码调用针对当前状态数据执行事务。为了使这些链码交互非常有效，所有键的最新值都存储在状态数据库中。

智能合约主要是`put`、`get`和`delete`世界状态，并且还可以查询状态变化历史。Chaincode“shim”API 实现了 [ChaincodeStubInterface](https://godoc.org/github.com/hyperledger/fabric/core/chaincode/shim#ChaincodeStubInterface) ，其中包含访问和修改分类帐的方法，以及在 chain code 之间进行调用的方法。主要方法有:

`***** GetState(key string) ([]byte, error)` 执行查询以检索关于对象当前状态的信息

`* PutState(key string, value []byte) error`在分类帐世界状态下创建新对象或修改现有对象

`* DelState(key string) error`从分类帐的当前状态中删除对象，但不删除其历史记录

`* GetStateByPartialCompositeKey(objectType string, keys []string) (StateQueryIteratorInterface, error)`根据给定的部分组合键查询总账中的状态

`* GetHistoryForKey(key string) (HistoryQueryIteratorInterface, error)`返回键值的历史记录。

所有这些方法都使用字符串键作为记录标识符，使用字节片作为状态值。大多数例子使用序列化为字节的 JSON 文档作为链码状态值。Hyperledger Fabric 支持 LevelDB as CouchDB 作为状态数据库，保存每个对象的最新状态。LevelDB 是嵌入在每个对等体中的默认键值状态数据库。CouchDB 是一个可选的替代外部状态数据库，具有更多特性——它支持针对链码状态的 JSON 文档的丰富查询，而 LevelDB 只支持针对键的查询。

## 使用 ChaincodeStubInterface 方法查询和更新状态

如许多示例所示，资产可以表示为复杂的结构——Golang structs，在进入 chaincode 状态之前，需要将这些结构封送到 JSON string
,在从状态接收之后，需要将其解组。

用`ChaincodeStubInterface`方法这些操作可以看起来像
[这个](https://github.com/IBM/build-blockchain-insurance-app/blob/master/web/chaincode/src/bcins/invoke_insurance.go):

在上面的示例中，智能合约代码显式执行许多辅助操作:

*   创建组合键
*   从 state 接收数据后解组数据
*   在将数据置于状态之前对其进行编组

# 使用 CCKit 进行链码状态操作

## 状态方法包装

[CCKit](https://github.com/s7techlab/cckit) ，用于创建和测试 Hyperledger Fabric golang chaincode 的库，包含用于处理 chain code 状态的`ChaincodeStubInteface`方法的[包装器](https://github.com/s7techlab/cckit/blob/master/state/state.go#L48)。该方法[简化了](/coinmonks/erc20-token-as-hyperledger-fabric-golang-chaincode-d09dfd16a339)链码状态下的链码键创建和数据转换。

## 在链码状态下操作时进行字节转换

状态包装器允许自动将 golang 类型封送到/来自字节片。这种类型可以是:

*   实现 [ToByter 和 FromByter](https://github.com/s7techlab/cckit/blob/master/convert/convert.go#L22) 接口的任何类型

*   Golang 结构或支持的类型之一(`int`、`string`、`[]string`)
*   [协议缓冲报文](https://developers.google.com/protocol-buffers/docs/gotutorial)

Golang structs [使用](https://github.com/s7techlab/cckit/tree/master/convert) [json 自动](https://golang.org/pkg/encoding/json/#Marshal)编组/解组。编组和
以及 [json。Umarshal](https://golang.org/pkg/encoding/json/#Unmarshal) 方法。[原型。玛莎](https://godoc.org/github.com/golang/protobuf/proto#Marshal) l 和 [proto。解组](https://godoc.org/github.com/golang/protobuf/proto#Unmarshal)用于转换 protobuf。

## 创建状态密钥

在 chaincode 数据模型中，我们经常需要在分类帐中存储一种类型的许多实例，例如多份商业票据、信用证等等。在这种情况下，这些实例的唯一键通常由属性组合构成，例如:

> ``CommercialPaper`` + `{Issuer}` + `{PaperId}`

产生一系列链码状态条目密钥

> [ ``CommercialPaperIssuer1Id1``，``CommercialPaperIssuer2Id2``，…]

可以在代码中定制创建实例主键的逻辑，基于几个属性的组合构造实例的组合键。然后，组合键可以用作普通的字符串键，使用`PutState()`和`GetState()`函数记录和检索值。

下面的代码片段显示了在`ChaincodeStubInterface`中创建和使用组合键的函数列表:

将数据放入链码状态或从链码状态获取数据时，必须提供密钥。 [CCKit](https://github.com/s7techlab/cckit) 有 3 个选项用于处理条目键:

*   键可以显式传递给`Put`方法

```
c.State().Put ( `my-key`, &myStructInstance)
```

*   按键类型可以实现`Keyer`接口

*   关键字可以是`Key`类型——本质上是字符串的切片，该切片将使用`shim.CreateCompositeKey` 方法自动转换为字符串。

而在 chaincode 中你只需要提供类型实例，key 就会自动创建:

```
c.State().Put (&myStructInstance)
```

*   条目类型可以有关联映射

映射为实体的名称空间(键的前缀)、主键和其他唯一键和非唯一键定义规则。映射主要与`protobuf` 状态模式一起使用。

## 范围查询

除了使用惟一键检索资产之外，`ChaincodeStubInterface`还为函数提供了根据一个范围标准检索资产集的机会。
此外，可以构建组合键，以支持针对键的多个组成部分的查询。

range 函数返回一个迭代器`StateQueryIteratorInterface`，遍历一组匹配查询标准的键。返回的键按词法顺序排列。
此外，当组合键有多个属性时，范围查询功能`GetStateByPartialCompositeKey()`可用于搜索匹配属性子集的键。

例如，由`Issuer`和`PaperId` 属性组成的`CommercialPaper`的关键字，只能从一个 `Issuer`中搜索条目。

# Protobuf 状态模型示例

CCKit 协议缓冲区[示例](http://../examples/cpaper)使用[商业票据场景](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/scenario.htm)和
实现与[节点相同的功能。来自官方文档的链码样本。Protobuf 是一种以高效且可扩展的格式编码结构化数据的方式。](https://github.com/hyperledger/fabric-samples/tree/release-1.4/commercial-paper/organization/digibank/contract)

## **Protobuf 模式优势:**

1.  **模式抽象层**

以`.proto`格式对业务对象的语义编码一次，就足以帮助确保消息不会在应用程序之间丢失，
并且您创建的边界会强制实施您的业务规则。

2.**扩展——验证器等**

Protobuf v3 不支持验证所需参数，但有第三方项目进行 proto 验证，例如
[https://github.com/mwitkow/go-proto-validators](https://github.com/mwitkow/go-proto-validators)。它允许在模式级别对数据结构的形状和有效性规则进行编码。

3.**简单的语言互操作性**
因为协议缓冲区是以多种语言实现的，它们使您的架构中多语言应用程序之间的互操作性变得更加简单。如果您使用 Java Fabric SDK 或 Node 引入新服务。Js Fabric SDK 您只需将`.proto`文件交给用目标语言编写的代码生成器，就可以保证这些架构之间的安全性和互操作性。

## 定义模型

使用协议缓冲区，您编写一个您希望存储的数据结构的`.proto`描述。由此，协议缓冲区编译器创建了一个 golang 结构，该结构使用有效的二进制格式(或`json`)实现了协议缓冲区数据的自动编码和解析。生成的类为组成协议缓冲区的字段提供 getters 和 setters，并作为一个单元处理协议缓冲区的读写细节。

在[商业票据示例](https://github.com/s7techlab/cckit/tree/master/examples/cpaper)中，我们首先定义消息，这些消息将以链码状态存储或用作事件:

*   `CommercialPaper`将以链码状态存储
*   `CommercialPaperId`定义商业票据报文的唯一标识部分
*   `IssueCommercialPaper`新商业票据发行时触发`issue`交易及事件有效载荷
*   `BuyCommercialPaper`商业票据变更主时触发`buy`交易事件有效载荷
*   `RedeemCommercialPaper`商业票据兑付时触发`redeem`交易和事件的有效负载

## 定义 protobuf 到链代码的状态映射

协议缓冲区消息到链码映射器可用于在链码状态下存储模式实例。每个模式类型(protobuf 或 struct)都可以有映射规则:

*   主键创建逻辑
*   命名空间逻辑
*   二级密钥创建逻辑

## 链码

在[链码](https://github.com/s7techlab/cckit/blob/master/examples/cpaper/chaincode.go)中，我们简单地使用从`.proto`文件中生成的结构，链码状态创建在映射中预定义。链代码实现使用 [CCKit 路由和中间件特性](/coinmonks/routing-and-middleware-for-developing-hyperledger-fabric-chaincode-written-in-go-90913951bf08)来构建代码。

## 试验

我们可以使用 [MockStub](https://github.com/s7techlab/cckit/tree/master/testing) 测试所有链代码用例场景:

关于链码单元测试和测试驱动开发的更多信息，你可以在这里阅读:

> [https://medium . com/coin monks/test-driven-hyperledger-fabric-golang-chain code-development-dbec 4c b 78049](/coinmonks/test-driven-hyperledger-fabric-golang-chaincode-development-dbec4cb78049)

链码也可以用 IDL 规范实现为服务。这允许为 chaincode 自动生成 SDK、REST API、swagger 规范和文档

> [https://medium . com/coin monks/service-oriented-hyperledger-fabric-application-development-32 e 66 f 578 F9 a](/coinmonks/service-oriented-hyperledger-fabric-application-development-32e66f578f9a)