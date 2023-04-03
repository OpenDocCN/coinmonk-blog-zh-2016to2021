# 如何设置你的 DAPPs 不使用“元掩码”插件，而是签署交易后端。

> 原文：<https://medium.com/coinmonks/how-to-setup-your-dapps-to-not-use-metamask-plugin-but-sign-the-transaction-backend-b8cb8808c9ae?source=collection_archive---------0----------------------->

![](img/e1f48417d8dd8659efb991e679ffea8c.png)

Source: Google

我最近在生产中推出了分散式应用程序(DAPPS)。虽然这是一次有趣但具有挑战性的骑行，但积累的知识非常好。因为从头开始开发 reactjs 应用程序并为生产做好准备是一个非常麻烦的过程。

我们正处于 Web 3.0 时代，是的，一切都变得去中心化了，我们正在回归基础，但是用美妙的技术(区块链)来实现交易的不变性和透明性。话虽如此，采用 DAPPS 并不容易，因为它需要很少的插件来执行交易。所以很多 DAPPs 都有一个扩展(插件)来支持日常操作。但是为什么呢？

为什么最终用户必须担心这些插件？，他们想要的只是使用服务并获得/支付费用，不是吗？

当我们开发 DAPPs 的时候，作为一个开发者要记住的最重要的事情是“最终用户”，在一天结束的时候，没有用户的牵引，即使你构建了超级棒的应用程序，它也只是一个废物！

在本文中，我将解释如何构建您的 DAPPs 来使用内容提供者&在幕后签署交易。因此，从用户的角度来看，这就像普通的应用程序，但他们实际上是在处理分散的应用程序。

功劳归于这个开发者:[点击这里](https://github.com/cmaliwal/send-ether-with-web3)

**先决条件**

1.  运行以太坊区块链的节点(对等方)。你可以自己设置，或者从 Infura 免费使用。https://infura.io/T4&这是完全免费的开源软件。你所需要的是建立一个项目，并获得相应网络(testnet / mainnet)的 API 密钥。
2.  Web3
3.  以太网

## 步骤 1)导入 web3 & ethereumjs-tx

```
const Web3 = require('web3');
const EthereumTx = require('ethereumjs-tx')
```

## 步骤 2)设置你的 Infura API 并将其传递给 parms-in fura & http provider，这里我使用的是 Ropsten Network

```
const infura = `https://ropsten.infura.io/v3/${process.env.INFURA_API_KEY}`;const web3 = new Web3(new Web3.providers.HttpProvider(infura));
```

## 步骤 3)设置关键变量，如帐户地址、私钥、ABI、合同地址，以备后用。

```
web3.eth.defaultAccount = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx';var abi = process.env.ABI;var pk  = process.env.PRIVATEKEY;  // private key of your accountvar toadd = process.env.WALLET_DESTINATION;var address = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'; //Contract Address
```

## 步骤 4)获取合同地址的 Nonce 值，这将有助于避免 RPC 错误

```
web3.eth.getTransactionCount(web3.eth.defaultAccount, function (err, nonce) {console.log(“nonce value is “, nonce);const contract = new web3.eth.Contract(JSON.parse(abi), address, {from: web3.eth.defaultAccount ,gas: 3000000,})
```

## 步骤 5)设置函数 ABI，这个变量基本上是从智能合约中调用您的函数。

```
const functionAbi = contract.methods.mymethodname(args).encodeABI();
```

这里-> args 表示方法需要的参数数量..因此，如果你有一个以上的参数，那么用“逗号”隔开

mymethodname ->在智能协定中表示您的方法名

## 步骤 6)设置选项/详细信息以签署您的交易

```
var details = {"nonce": nonce,"gasPrice": web3.utils.toHex(web3.utils.toWei('47', 'gwei')),"gas": 300000,"to": address,"value": 0,"data": functionAbi,};
```

这里，“47”是汽油价格。我建议你从加油站把它拉出来，这样它是自动的&设置当天的价格。

“收件人”->您可以将其设置为合同地址或“其他钱包账户”。

“gas”->“gas limit”，您可以使用“estimateGas”方法再次将其设置为拉。目前，它是硬编码的

## 步骤 7)调用 EthereumTx 并传递详细信息以创建事务对象

```
const transaction = new EthereumTx(details);
```

## 步骤 8)使用“发送者”帐户的私钥签署交易

```
transaction.sign(Buffer.from(pk, ‘hex’))
```

## 第 9 步)序列化值并将其转换成要传递的原始数据

```
var rawdata = ‘0x’ + transaction.serialize().toString(‘hex’);
```

## 步骤 10)最后调用 sendSignedTransaction 到区块链，如下所示。

```
web3.eth.sendSignedTransaction(raw).on(‘transactionHash’, function(hash){ console.log([‘transferToStaging Trx Hash:’ + hash]);}).on(‘receipt’, function(receipt){ console.log([‘transferToStaging Receipt:’, receipt]);}).on(‘error’, console.error);
```

现在，您可以在不调用元掩码的情况下调用事务了！一切都在幕后进行。用户现在不需要安装/设置元掩码插件。

注意:当用户只想使用所提供的服务，并且交易仅由一个使用发送者私钥的帐户签名时，此示例有效。

如果你想用各自用户的私钥签署每一笔交易，那就不一样了。

检查下面的完整代码

```
instantiateContract = (args) =>{const infura = `https://ropsten.infura.io/v3/${process.env.INFURA_API_KEY}`;const web3 = new Web3(new Web3.providers.HttpProvider(infura));web3.eth.defaultAccount = process.env.ACCOUNT_ADDRESS;var abi = process.env.ABI;var pk  = process.env.PRIVATEKEY;  // private key of your accountvar toadd = process.env.WALLET_DESTINATION;var address = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'; //Contract Addressweb3.eth.getTransactionCount(web3.eth.defaultAccount, function (err, nonce) {console.log(“nonce value is “, nonce);const contract = new web3.eth.Contract(JSON.parse(abi), address, {from: web3.eth.defaultAccount ,gas: 3000000,})const functionAbi = contract.methods.mymethodname(args).encodeABI();var details = {“nonce”: nonce,“gasPrice”: web3.utils.toHex(web3.utils.toWei('47', ‘gwei’)),“gas”: 300000,“to”: address,“value”: 0,“data”: functionAbi,};const transaction = new EthereumTx(details);transaction.sign(Buffer.from(pk, ‘hex’) )var rawData = ‘0x’ + transaction.serialize().toString(‘hex’);web3.eth.sendSignedTransaction(rawData).on(‘transactionHash’, function(hash){console.log([‘transferToStaging Trx Hash:’ + hash]);}).on(‘receipt’, function(receipt){console.log([‘transferToStaging Receipt:’, receipt]);}).on(‘error’, console.error);});}
```

注:喜欢这篇文章？给 Logeswaran 竖起大拇指(鼓掌)并在 Linkedin 上关注他

如果你有其他方法让 DAPPs 更加用户友好，请在下面评论..

如果您需要任何帮助来成功设置，请联系我。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)