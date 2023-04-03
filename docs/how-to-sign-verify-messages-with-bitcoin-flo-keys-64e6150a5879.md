# 使用具有 flo/比特币密钥的 bitcoinjs 对消息进行签名验证

> 原文：<https://medium.com/coinmonks/how-to-sign-verify-messages-with-bitcoin-flo-keys-64e6150a5879?source=collection_archive---------4----------------------->

![](img/1834e0110414cacab0b503984420ded0.png)

**目标** —到本文结束时，你将能够用比特币或任何其他区块链的公钥-私钥对对信息进行数字签名。 [FLO 区块链](http://flo.cash)在例子中使用。使用 nodejs 的 [bitcoinjs-lib](https://github.com/bitcoinjs/bitcoinjs-lib) 和 [bitcoinjs-message](https://github.com/bitcoinjs/bitcoinjs-message) 模块。

# 什么是数字签名？

# **代码**

## 预设置

在撰写本文时，2018 年 8 月 13 日， [bitcoinjs-message](https://github.com/bitcoinjs/bitcoinjs-message) 仅适用于版本 3.x.x 的 [bitcoinjs-lib](https://github.com/bitcoinjs/bitcoinjs-lib) 。

```
mkdir projectDir
cd projectDir
npm init                          // creates packages.json file
npm install bitcoinjs-lib@3.3.2   // Install specific version 
npm install bitcoinjs-message
```

我还假设您随身携带了公钥和私钥对。请在评论中告诉我是否应该对脚本进行修改，以显示如何生成公钥-私钥。

## 主脚本

我目前使用 FLO testnet 地址为以下脚本。您可以通过指定正确的地址生成参数 ie 来使用任何您想要的网络。pubKeyHash、scriptHash、wif(密钥)、公共和私有头。对于比特币和基于比特币的区块链，这些值可以在 **chainparams.cpp** 文件中找到。

```
var bitcoin = require('bitcoinjs-lib') // v3.x.x
var bitcoinMessage = require('bitcoinjs-message')// Parameters required for FLO address generation
const FLOTESTNET = {
  messagePrefix: '\x19FLO testnet Signed Message:\n',
  bip32: {
    public: 0x013440e2,
    private: 0x01343c23
  },
  pubKeyHash: 0x73,
  scriptHash: 0xc6,
  wif: 0xef
}// Sign a message
// For Bitcoin network do not specify any network, its defaultvar keyPair = bitcoin.ECPair.fromWIF('cRgnQe9MUu1JznntrLaoQpB476M8PURvXVQB5R2eqms5tXnzNsrr', FLOTESTNET)
var privateKey = keyPair.d.toBuffer(32)
var message = 'Hey this is Ranchi Mall'var signature = bitcoinMessage.sign(message, privateKey, keyPair.compressed)
console.log(signature.toString('base64'))
// => 'ILqvGGBI89K8Tk9/BgrGPSMTB9ZY+Z88Z0GjVsx7uPTwOfQ+eNj/VZKZ40iSbUPgz6mSBvo6w1Dkzns9DqfYa2o='// Verify a message
var address = 'oWwrvqa3QP5EHHBiUn9eAQf7d1ts5BnChG'
var signature = 'ILqvGGBI89K8Tk9/BgrGPSMTB9ZY+Z88Z0GjVsx7uPTwOfQ+eNj/VZKZ40iSbUPgz6mSBvo6w1Dkzns9DqfYa2o='
var message = 'Hey this is Ranchi Mall'console.log(bitcoinMessage.verify(message, address, signature))
// => true
```

# 参考

*   [bitcoinjs-message 的 Github 库](https://github.com/bitcoinjs/bitcoinjs-message)

有问题吗？评论？建议？请在此留言或点击 ivek.teega@gmail.com[*v*](/)*联系我。我是兰契商城的开发人员。*