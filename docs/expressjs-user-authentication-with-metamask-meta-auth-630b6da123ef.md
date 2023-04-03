# 使用元掩码和元身份验证的 Expressjs 用户身份验证

> 原文：<https://medium.com/coinmonks/expressjs-user-authentication-with-metamask-meta-auth-630b6da123ef?source=collection_archive---------0----------------------->

去年三月，MetaMask 的维护者 Dan Finlay 写了[在你的浏览器](/metamask/the-new-secure-way-to-sign-data-in-your-browser-6af9dd2a1527)中签署数据的新的安全方法。

他的代码示例对从服务器发送的消息进行签名和身份验证。这使得元掩码能够处理用户名和密码的用户验证。此外，使用元掩码创建用户帐户不需要个人识别信息。它与你的以太坊钱包具有相同的安全级别——因为它是一个以太坊钱包。

![](img/6f528abda272dc7a6fbb931573b54855.png)

它是这样工作的。服务器生成询问消息。您的浏览器请求询问消息。MetaMask 使用您的以太坊私钥对消息进行签名。服务器从签名的消息中重新创建以太坊地址。

这与以太坊交易的安全性相同。MetaMask 对文本数据进行签名，而不是以太坊交易数据。就像你的私人密钥签署花费你的硬币一样，它签署它收到的服务器消息。该文本可以是随机的，服务条款或其他完全。

使用您的加密签名，服务器能够在您每次不使用用户名或密码访问时验证您是同一个唯一的用户。

你可以在 [Github Repo](https://github.com/I-Gave/meta-auth) 上找到`meta-auth`的源代码。或者了解更多关于 [npm](https://www.npmjs.com/package/meta-auth) 的信息。

它是这样工作的，

# 后端

首先，安装`express`和`meta-auth`

```
$ npm install express meta-auth
```

初始化两个模块

```
const express = require('express');const MetaAuth = require('meta-auth');const app = express();const metaAuth = MetaAuth();
```

默认情况下，`meta-auth`将观察三个路线参数

1.  MetaAddress —用于发布地址的质询消息。
2.  元消息—客户端返回的质询消息。
3.  元签名-已签名的质询与元消息一起返回。

初始化`meta-auth`时，这些参数名称是可配置的

```
const metaAuth = require('meta-auth')({ message: 'msg', signature: 'sig', address: 'address'});
```

包括 metaAuth 中间件。第一个函数发出一个以太坊地址的挑战。如果指定了:MetaAddress 路由参数，中间件将创建具有`challenge`属性的`req.metaAuth`对象。

第二个验证消息签名，并为`req.metaAuth`提供一个`recovered`属性。如果地址被恢复，服务器具有密码证明该地址属于用户。让他们登录是安全的。

```
app.get('/auth/:MetaAddress', metaAuth, (req, res) => { // Request a message from the server res.send(req.metaAuth.challenge)});app.get('/auth/:MetaMessage/:MetaSignature', metaAuth, (req, res) => { if (req.metaAuth.recovered) { // Signature matches the cache address/challenge // Authentication is valid, assign JWT, etc. res.send(req.metaAuth.recovered); } else { // Sig did not match, invalid authentication res.status(500).send(); };});
```

下面是引擎盖下发生的事情

```
function createChallenge (address) { const hash = crypto.createHmac('sha256', secret) .update(address + uuidv4()) .digest('hex'); cache.set(address, hash); return hash;}
```

首先，客户机将其地址发送给服务器。它创建地址和 uuidv4 的盐散列。它缓存地址/挑战对，以便以后查找。

```
function checkChallenge(challenge, sig) { const data = [{ type: 'string', name: 'challenge', value: challenge }] const recovered = sigUtil.recoverTypedSignature({ data, sig }); const storedChallenge = cache.get(recovered); if (storedChallenge === challenge) { cache.del(recovered); return recovered; } return false;}
```

挑战和签名返回给服务器。它从签名中恢复地址。然后确保质询是针对地址的。然后从高速缓存中移除数据并返回恢复的地址。

返回快速验证路线

```
app.get('/auth/:MetaMessage/:MetaSignature', metaAuth, (req, res) => { if (req.metaAuth.recovered) { // Signature matches the cache address/challenge // Authentication is valid, assign JWT, etc. res.send(req.metaAuth.recovered); } else { // Sig did not match, invalid authentication res.status(400).send(); };});
```

如果找到了`recovered`属性，`meta-auth`成功地从消息签名中恢复了地址。

从这里，启动用户的会话，分配一个 JWT，让他们访问他们的数据，无论你想做什么。

# 前端

至此，您已经知道客户机将向服务器发出两个请求

1.  接收基于以太坊地址的挑战
2.  用签名返回挑战。

这是针对服务器的，但是客户端需要在这期间第三次调用它的 web3 提供者。

(代码可以在[这里找到](https://github.com/I-Gave/meta-auth/tree/master/examples))

```
$.get('http://localhost:3000/auth/' + web3.eth.accounts[0], (res) =>   { challenge = [{ type: 'string', name: 'challenge', value: res }]; const from = web3.eth.accounts[0]; const params = [challenge, from]; const method = 'eth_signTypedData'; web3.currentProvider.sendAsync({ method, params, from }, async (err, result) => { signature = result.result; if (err) { return console.error(err); } if (result.error) { return console.error(result.error); } $('.signature').text(signature); });});
```

从服务器接收质询，MetaMask 将处理 sendAsync 调用以签署所提供的质询。

```
$.get('http://localhost:3001/auth/' + challenge[0].value + '/' + signature, (res) => { if (res === web3.eth.accounts[0]) { $('.success').show(); } else { $('.fail').show();  }});
```

为了验证，客户端返回相同的挑战和他们的签名。如果服务器响应与用户的以太坊地址匹配，则认证成功。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)

你可以在 [Github Repo](https://github.com/I-Gave/meta-auth) 上找到`meta-auth`的源代码。或者了解更多关于 [npm](https://www.npmjs.com/package/meta-auth) 的信息