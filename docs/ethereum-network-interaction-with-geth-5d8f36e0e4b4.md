# 以太坊网络与 Geth 的交互

> 原文：<https://medium.com/coinmonks/ethereum-network-interaction-with-geth-5d8f36e0e4b4?source=collection_archive---------1----------------------->

假设到目前为止您已经完成了[供应](https://concurrence.io/exploration/provisioning/)部分，现在是时候使用 Geth 与以太坊交流了。

Geth 启动并与区块链同步后，您可以通过以下方式连接到它:

```
geth attach [http://:8545](/)Welcome to the Geth JavaScript console! instance: Geth/v1.7.1-stable-05101641/linux-amd64/go1.9 
coinbase: 0x0e1e9ce68b3254461a95916c185c37dd65468ab5 
at block: 1856113 (Thu, 12 Oct 2017 13:25:06 UTC)  
 modules: eth:1.0 net:1.0 personal:1.0 rpc:1.0 web3:1.0 >
```

全新安装的 Geth 还没有任何帐户:

```
> eth.accounts []
```

您可以使用 **newAccount()** 功能创建账户:

```
> web3.personal.newAccount() 
Passphrase: 
Repeat passphrase: 
"0x4ffd642a057ce33579a3ca638347b402b909f6d6" 
> 
> web3.personal.newAccount() 
Passphrase: 
Repeat passphrase: 
"0xa3eebd575245e0bd51aa46b87b1ffc6a1689965a"
```

您可以通过 **getBalance()** 获取任意账户的余额:

```
> eth.getBalance("0x4ffd642a057ce33579a3ca638347b402b909f6d6") 0
```

也可以访问 [etherscan.io](https://ropsten.etherscan.io/tx/0x3e5bfb4bc071e4b0e70eedbc7b667ff6a5e7eb5397a21e16582b85848190ae98) 查看地址。

要得到测试网乙醚，你可以简单地问水龙头要一些:

```
curl -X POST  \   
  -H "Content-Type: application/json" \   
  -d "{\"toWhom\":\"0x4ffd642a057ce33579a3ca638347b402b909f6d6\"}" \
   [https://ropsten.faucet.b9lab.com/tap](https://ropsten.faucet.b9lab.com/tap){   
"txHash" : "0x3e5bfb4bc071e4b0e70eedbc7b667ff6a5e7eb5397a21e16582b85848190ae98" }
```

您将获得一个事务哈希，并且您可以[在 etherscan](https://ropsten.etherscan.io/tx/0x3e5bfb4bc071e4b0e70eedbc7b667ff6a5e7eb5397a21e16582b85848190ae98) 上跟踪它。

挖掘交易后，您将看到余额增加:

```
> eth.getBalance("0x4ffd642a057ce33579a3ca638347b402b909f6d6") 500000000000000000
```

您现在可以通过以下方式向另一个帐户发送乙醚:

```
eth.sendTransaction(   
 {     
  from:"0x4ffd642a057ce33579a3ca638347b402b909f6d6",     
  to:"0xa3eebd575245e0bd51aa46b87b1ffc6a1689965a",     
  value:100000000000000000   
 } 
)
```

同样，会返回一个事务哈希，您可以[在 etherscan](https://ropsten.etherscan.io/tx/0x3b62b1434591bd6d7e9dc21594a28193c68ff0be756edff0e6f6e62e4cb3343f) 上跟踪它。

交易完成后，我们可以看到两个帐户的余额:

```
> eth.getBalance("0x4ffd642a057ce33579a3ca638347b402b909f6d6") 
399370000000000000 
> 
> eth.getBalance("0xa3eebd575245e0bd51aa46b87b1ffc6a1689965a") 
100000000000000000
```

您可能还想将这些大数包装在一个 **fromWei()** 函数中:

```
web3.fromWei(
 eth.getBalance(
   "0x4ffd642a057ce33579a3ca638347b402b909f6d6"
 ),"ether"
)0.39937
```

注意，0x4ffd 并不正好有 4 个以太。这是因为少量的乙醚被消耗在汽油上来为交易提供燃料。在下一部分，我们将[部署一份合同并讨论天然气](https://concurrence.io/exploration/deployingacontract/)。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)

这是摘自[concurrency——以太坊网络互动](https://concurrence.io/exploration/ethereuminteraction/)。

点击阅读更多关于并发的信息。