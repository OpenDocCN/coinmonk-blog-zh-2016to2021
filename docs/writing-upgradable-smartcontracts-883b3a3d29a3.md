# 可升级智能合同编码

> 原文：<https://medium.com/coinmonks/writing-upgradable-smartcontracts-883b3a3d29a3?source=collection_archive---------1----------------------->

![](img/3224b15e1a9ceb95e54929c402007962.png)

## 升级智能合同意味着什么？

作为开发人员，我们一直想升级我们的代码，引入新的特性，同时修复错误。谈到区块链，写在区块链身上的任何东西都是不可改变的。即使是智能合同，一旦在区块链网络上部署，我们也不能更改或修改它。在过去的几年里，我们已经看到了许多攻击，比如 **DAO，再进入攻击，平价钱包黑客**仅仅因为智能合同中的小漏洞就造成了数百万以太网的损失。所以在部署之前对智能合同进行几十次测试对于避免这些漏洞非常重要。如果我们想为已部署的智能合同引入新功能呢？因为它甚至不可能与区块链，对不对？但不知何故，有一种方法可以升级我们的合同。怎么了？"一种没有实际升级的升级方式."似乎很有趣？是的，等我一下，你可能已经知道你的智能手机中的**呼叫转移**功能了，一旦你的移动号码和你的首选新号码的呼叫转移被激活，每个打到你的移动号码的呼叫都会被转移到你的首选移动号码。同样，可升级的合同也是有效的。您的旧合同处理数据，并将到达您的旧合同地址的每个函数调用重定向到新部署的合同 ie..升级版的。

有充分的理由，gochain 为升级合同提供了 CLI 协议。那你还在等什么？我们走吧。

在演示中，我将把简单的迎宾合同从 v1 升级到 v2。在 v1 版本中，没有 getter 方法来返回问候语。在 v2 中，getter 方法是作为 bug 修复或新特性添加的，无论您称其为什么。代码可用[此处为](https://github.com/Salmandabbakuti/Smartcontract-Upgrades)。

## 环境

Ubuntu 桌面，带有 gochain web3 库。

## 先决条件

1.gochain Web3 库

```
curl -LSs https://raw.githubusercontent.com/gochain-io/web3/master/install.sh | sh
```

2.Gochain 钱包和一些 GO-token。

去[https://explorer.gochain.io/wallet/create](https://explorer.gochain.io/wallet/create)把你的私人钥匙存放在其他安全的地方。

要在网络上进行任何操作，您都需要令牌。所以为了得到 testnet 令牌，加入他们的官方电报频道[https://t.me/gochain_testnet](https://t.me/gochain_testnet)并要求他们通过粘贴你之前创建的钱包地址**来提供一些 testnet 令牌

# 步伐

## 1.环境设置

a)。将私钥导出到您的 Env。变量(需要根用户权限)

`sudo -s`

```
export WEB3_PRIVATE_KEY=0x...
```

通过这个命令检查您的私钥是否正确`$ web3 myaddress`如果这个命令返回您在先决条件中创建的钱包地址，那么您就可以开始了。

b)。使用 Go-Chain testnet

```
$ export WEB3_NETWORK=testnet
```

## 2.部署可升级合同

a)。编制合同

将 greeter.sol 文件复制到您的目录中，然后

```
web3 contract build greeter.sol
```

b)。部署合同

```
web3 contract deploy --upgradeable greeter.bin
```

部署后，您将获得合同地址，将该地址添加到 env。变量

```
export WEB3_ADDRESS=0x...
```

部署可升级合同实际上部署了两个合同。原始迎宾合同和代理合同，将呼叫重定向到升级的合同。用这个命令检查我们代理契约重定向调用和存储的位置

```
web3 contract target
```

c)。书写交易(问候语)

```
web3 contract call --abi greeter.abi --function greet "Hi Guys, This is from Old Contract"
```

交易完成后，我们成功地在区块链上添加了问候语。但是，仍然没有返回附加问候的选项。这就是为什么我们试图用新代码升级合同。

## 3.升级合同

将 greeterv2.sol 复制到您的目录中，

```
web3 contract build greeterv2.sol
web3 contract deploy greeter.bin
```

一旦部署，它将返回新部署的合同地址。使用这个地址我们可以升级我们的旧合同`greeter`到`greeterv2.`运行下面的命令来完成最后一步

```
web3 contract upgrade --to 0x.... //add your newly deployed contract address here
```

现在，您可以使用相同的地址调用新合同。在旧契约中，变量没有 getter 函数，在新契约中，我们增加了 getter 函数`getGreeting`。现在您可以使用这个命令调用它。

```
web3 contract call --abi greeter.abi --function getGreeting>Hi Guys, This is from Old Contract
```

## 4.暂停和恢复合同

可升级合同还包括暂停和恢复执行的能力。如果您在契约中发现了任何 bug，您可以使用这些命令简单地暂停(或稍后恢复)契约

```
web3 contract pause
web3 contract resume
```

一旦暂停，如果你调用这个函数，你将得到`unmarshalling empty output`字符串，直到你恢复合同。

嘣…

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)