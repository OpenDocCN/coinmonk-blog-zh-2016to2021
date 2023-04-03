# 使用 IOTA:解析供应链项目的数据

> 原文：<https://medium.com/coinmonks/playing-with-iota-parsing-data-for-supply-chain-project-b46806170c6?source=collection_archive---------3----------------------->

大家好，我是 IOTA development 的新人，我决定使用 IOTA 开发一个供应链系统。该项目将通过为每个组织提供消耗环保材料的比率，迫使组织使用环保材料，并持续跟踪产品，直到我们将其回收利用。如果你不熟悉 IOTA，我推荐你阅读[文档](https://docs.iota.org/docs/getting-started/0.1/tutorials/get-started)，你应该知道 Nodejs 的基础知识。

在设计我的项目想法时，我制作了一个简单的原型，用于建立一个安全的连接来跟踪所有产品。

**我会做什么？**

1-创建一个定制的 ID，表示环保材料生产商、买方和产品订单(例如:第一个创建的产品、第二个创建的产品..等等)

2-这个 ID 就像每个产品上的条形码。

3-每次条形码阅读器检测到一件产品。它将发送一个带有产品 ID、当前条形码阅读器 ID 和当前时间的 JSON 对象。所以我可以跟踪大量的产品。

**我的原型如何建立通信？**

1-发送内容为“初始化根”的受限 MAM 消息，并返回根地址。

2-使用受限的 MAM 将所有产品作为 JSON 对象发送。

3-将根地址作为公共事务发送。

4-从公共事务中获取根地址。

5-使用侧键将它解析到所有设备，以获取所有产品 JSON 对象。

**代码实现:**

首先，我们需要安装 iota 包

```
npm install @iota/corenpm install @iota/converter
```

运筹学

你可以使用`yarn add`

现在，创建您的 JavaScript 文件`touch SendPublicTransaction`

导入包并将其连接到区块链。

```
const iotaLibrary = require(‘@iota/core’)const Converter = require(‘@iota/converter’)const iota = iotaLibrary.composeAPI({provider: ‘https://nodes.devnet.thetangle.org:443'})
```

要发送公共数据，我假设你熟悉种子和地址，如果注意检查这个[链接](https://docs.iota.org/docs/getting-started/0.1/tutorials/get-started#step-1-create-a-seed)。我们需要创建一个带有地址目的地和消息内容的传输对象，并确保我们案例中的传输值为零。

```
const message = Converter.asciiToTrytes(_message)const transfers = [{value: 0,address: _address, // Where the data is being sentmessage: message // The message converted into trytes}]iota.prepareTransfers(_seed, transfers).then(trytes => iota.sendTrytes(trytes, 3, 14)).then(bundle => {console.log(‘Transfer successfully sent’)bundle.map(tx => console.log(tx))}).catch(err => {console.log(err)})}
```

**获取此消息:**

```
const FetchPublicTransaction = async (_address) =>{return new Promise(function(resolve, reject) {iota.findTransactionObjects({ addresses: [_address] },function(error, response) {if (error) {reject(error);} else {console.log(‘Encoded message:’)console.log(response[0].signatureMessageFragment)// Modify trytes into a consumable lengthconst trytes = response[0].signatureMessageFragment.slice(0, -1)//Convert trytes to plan text//Convert trytes to plan textconst data = Converter.trytesToAscii(trytes)console.log(‘Decoded message:’)console.log(data)resolve(data)}})})}
```

如果你在理解这段代码时遇到困难，请查看 IOTA 基金会制作的视频[来详细解释它。我补充了一些承诺如果你不知道是什么检查这个](https://www.youtube.com/watch?v=Z-NN0rRcwY0&feature=youtu.be&t=179)[链接](https://codeburst.io/javascript-promises-explained-with-simple-real-life-analogies-dd6908092138)。

然后我们有受限的 MAM 代码

**初始化 MAM**

```
const mamType = ‘restricted’const mamSecret = ‘DONTSHARETHISPASSWORD’mamState = Mam.changeMode(mamState, mamType, mamSecret
```

发送一条短信

```
const Publish = async data => {// Convert the JSON to trytes and create a MAM messageconst trytes = asciiToTrytes(data)const message = Mam.create(mamState, trytes)// Update the MAM state to the state of this latest messagemamState = message.state// Attach the messageawait Mam.attach(message.payload, message.address, 3, 9)return message.root}
```

使用 `foreach`发送多条信息

```
const PublishAll = (noOfMessages)=>{const products = []for (let index = 0; index < noOfMessages; index++) {products.push({id: index,time: (new Date()).toLocaleString()})}Array.prototype.forEach.call(products,product =>{SendMamRestricted.execute(JSON.stringify(product))})}
```

**获取所有受限的 MAM 消息:**

```
const FetchMam = async (_root) => {// Callback used to pass data + returns next_rootconsole.log(“inside FetchMamRestricted function root is”,_root) // outptu ‘somthing’const resp = await Mam.fetch(_root, mamType, mamSecret, logData)console.log(resp)}
```

现在你需要做的就是连接所有的代码。

sendID.js 文件:

```
const SendMamRestricted = require(“./SendMamRestricted.js”)const SendPublicTransaction= require(“./SendPublicTransaction.js”)const PublishAll = require(‘./PublishAll.js’)const seed = ‘any 81 capital char and 9’const address = ‘any 81 capital char and 9’SendMamRestricted.execute(“initialize root”).then(function(result) {SendPublicTransaction.execute(seed,address,result)PublishAll.execute(10)})
```

fetchID.js 文件:

```
const FetchPublicTransaction = require(‘./FetchPublicTransaction.js’)const FetchMamRestricted = require(‘./FetchMamRestricted.js’)const address = ‘any random 81 capital char and 9’const main = async()=>{const root = await FetchPublicTransaction.execute(address)// retrun rootawait FetchMamRestricted.execute(root.substring(0,root.length-1012))}main()
```

在我的 Github 链接上找到所有代码:[https://github.com/yehia67/Products-Tracker-Prototype/](https://github.com/yehia67/Products-Tracker-Prototype/)

如果你有任何问题，请问我。谢谢大家！

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)