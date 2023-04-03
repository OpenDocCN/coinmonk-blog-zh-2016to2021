# 移动中的链码 Hyperledger 结构区块链上的智能合约

> 原文：<https://medium.com/coinmonks/chaincode-on-the-go-smart-contracts-on-the-hyperledger-fabric-blockchain-82dd61b3c669?source=collection_archive---------1----------------------->

![](img/ea060d5feeeca4495c74b35f0d382696.png)

Image courtesy of pixabay.com

关于区块链的简单英语介绍，请阅读[商务人士区块链](https://www.linkedin.com/pulse/blockchain-businessperson-rammohan-raja)。关于 Fabric(Hyperledger Fabric 的简称)的简要概述，请浏览 [Hyperledger Fabric 为企业带来区块链](https://www.linkedin.com/pulse/hyperledger-fabric-brings-blockchain-enterprise-rammohan-raja)。

> [发现并回顾最佳区块链软件](https://coincodecap.com)

我花了十年甚至更长时间才最终将我的链表知识付诸实践。如果不是区块链科技，我到死都不会使用我的链表技能。下次有人在那些悲伤的面试中问你链表的时候，你可以满足于它在你的职业生涯中确实有一些实际用途。

> *最后……一个现实生活中的链表用例。*

区块链实际上是一系列数据块。就像链表中的节点一样，区块链中的块使用指针/引用相互链接。您从最近的块(链表的头节点)开始，可以遍历链一直到起源块。像链表中的节点一样，区块链中的块中包含一些数据(例如事务数据)，前一个块的安全散列和当前块中所有数据的安全散列。

您最好使用现有的实现，而不是从头开始构建自己的区块链。Fabric 是 IBM 支持的开源区块链实现。这是一个允许在现实世界的商业应用中使用的区块链。Fabric 提供对数据的受控访问，同时仍提供区块链的所有优势，如共享分类帐、智能合同、隐私和共识。本文将停留在一个相对较高的水平，以尽可能保持学习曲线的正常。为了进一步缩小范围，我将集中讨论与开发人员相关的那些方面。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)

> *链码≈存储过程*

开发人员编写与共享账本交互的链码(结构世界中的智能合约)。一个恰当的类比是用于操作数据库中数据的存储过程。Fabric 是用 Go 语言写的。您可能已经猜到了，第一个支持编写智能合同/链码的语言也是 Go。支持其他语言的工作正在进行中。但是作为一个积极努力的人，我们将学习 Go 语言，并撰写我们的第一份智能合同。

我们生活在一个计算机语言变得像好莱坞作品一样的世界里。甚至在一种语言成为热门之前，一种新的语言就已经发布了。计算机语言的权力游戏似乎是趋势。掌握这些语言中的任何一种都没有意义。当你这么做的时候，另一个人可能已经废黜了旧的最爱。

分类帐上的所有交易(查询或更新)都是通过链码完成的。允许访问链码。这样，我们可以确保医生可以运行链码来查看病历，但其他人不能。药剂师只能运行链码来查看处方，而医生可以运行链码来开处方。

```
func (s *SmartContract) Init(APIstub shim.ChaincodeStubInterface) sc.Response {
          return shim.Success(nil)
}

func (s *SmartContract) Invoke(APIstub shim.ChaincodeStubInterface) sc.Response {

          function, args := APIstub.GetFunctionAndParameters()

          if function == "queryCar" {
                   return s.queryCar(APIstub, args)
          } else if function == "initLedger" {
                   return s.initLedger(APIstub)
          } else if function == "createCar" {
                   return s.createCar(APIstub, args)
          } else if function == "queryAllCars" {
                   return s.queryAllCars(APIstub)
          } else if function == "changeCarOwner" {
                   return s.changeCarOwner(APIstub, args)
          }

          return shim.Error("Invalid Smart Contract function name.")
}
```

这是一个简单链码实现的框架。它有两个函数 Init 和 Invoke。当首次安装链码或升级现有链码时，会调用 Init。例如，在首次安装链码时创建资产。当我们需要链码来查询或修改共享的 leger 时，调用 Invoke。这两个函数都引用了 shim API (shim。ChaincodeStubInterface)，它实际上用于查询或更新共享分类帐。共享分类帐的状态更改仅在交易被验证并成功提交后生效。这里的[显示了完整的样本以及执行它们的指令。](http://hyperledger-fabric.readthedocs.io/en/latest/chaincode4ade.html#pulling-it-all-together)

虽然我们只能在 Go 中编写链代码，但我们可以使用多种语言的多个 API 来调用它们。例如，这就是使用 JavaScript 调用链码的方式。

```
var options = {
    wallet_path: path.join(__dirname, './network/creds'),
    user_id: 'PeerAdmin',
    channel_id: 'mychannel',
    chaincode_id: 'fabcar',
    network_url: 'grpc://localhost:7051',
};

const request = {
      chaincodeId: options.chaincode_id,
      txId: transaction_id,
      fcn: 'queryAllCars',
      args: ['']
};
```

完整的来源可在[这里](https://github.com/hyperledger/fabric-samples/blob/release/fabcar/query.js)。上例调用了 *fabcar* 智能合约中的以下函数。

```
func (s *SmartContract) queryAllCars(APIstub shim.ChaincodeStubInterface) sc.Response {

        startKey := "CAR0"
        endKey := "CAR999"

        ................
        ................
        ................

        return shim.Success(buffer.Bytes())
}
```

显而易见，为 Fabric 编写链代码并不是什么难事。但是建立网络并让它运行起来并不容易。Fabric project 在[记录](http://hyperledger-fabric.readthedocs.io/en/latest/build_network.html)步骤方面做得非常出色。我不打算在这里重复。所以，留出几天时间，耐心等待吧！

[![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)