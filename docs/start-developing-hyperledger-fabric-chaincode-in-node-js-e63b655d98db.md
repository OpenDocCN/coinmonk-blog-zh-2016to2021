# 在 Node.js 中开始开发 Hyperledger 结构链代码

> 原文：<https://medium.com/coinmonks/start-developing-hyperledger-fabric-chaincode-in-node-js-e63b655d98db?source=collection_archive---------0----------------------->

![](img/6eb8f5a356d854972381f0892b6c40be.png)

Photo by [Negative space](https://www.pexels.com/@negativespace) at [pexels.com](https://www.pexels.com/)

**什么是 Hyperledger 结构术语中的链码？？**

在 Hyperledger Fabric 中，Chaincode 是运行在网络对等体之上的一段代码，用于实现应用程序如何与分类帐交互的业务逻辑。当一个交易被提议时，它触发决定什么状态改变应该被应用到分类帐的链码。因此，为了在 Hyperledger Fabric 上开发分散式应用程序，必须编写名为 Chaincode 的应用程序逻辑。

Chaincode 可以用 Go，Nodejs，Java 写。与其他两种语言相比，Node.js 是一种更多人理解并更容易使用的语言，包括我在内。但是，仍然编写和部署 Node.js 链代码有点棘手，因为其文档中可用的信息非常少。因此，我决定编写 Node.js 链码的一些概念，并逐步编写和部署简单的链码。

## **总账结构数据库**

在深入到 Chaincode 之前，首先我们来看看数据在 Hyperledger Fabric Network 中存储在哪里。Hyperledger Fabric 使用键值数据库来存储其状态。默认情况下，fabric 使用 LevelDB。这个数据库保存特定对象的二进制数据，可以使用它的键进行查询。与传统的数据库集中不同，[区块链](https://blog.coincodecap.com/tag/blockchain/)数据库在网络的每一个节点上。因此，它被称为分散网络。

除了 LevelDB，还有另一个用于 Hyperledger Fabric 的数据库，名为 CouchDB。CouchDB 是一个可选的外部可插拔状态数据库。像 LevelDB 键值存储一样，CouchDB 可以存储任何用 chaincode 建模的二进制数据。但是作为一个 JSON 文档存储，CouchDB 还支持针对链码数据的丰富查询，当链码值(例如资产)被建模为 JSON 数据时。

## **链码组件**

1.fabric-contract-api :

用于实现智能合约的高级合约 API(作为 npm 模块提供)

织物垫片:

用于实现智能合约的低级合约 API(作为 npm 模块提供)

我们可以把 **fabric-shim** 看作是 **fabric-contract-api** 的缩减版本。对新版本的 fabric 使用高级 API 是一个很好的做法。但是， **fabric-contract-api** 可以做 shim 能做的一切。当然不止这些。

3 .存根:

它是 **fabric-contract-api** 中的一个接口，用于访问和修改账本(数据库状态)。因此，它是读写分类帐数据的主要链码接口。怎样才能读写数据？？让我们来看看存根接口中的一些常用方法

## **存根接口常用方法**

1. **getState(k):**

众所周知，Hyperledger Fabric 数据库以键值对的形式存储数据。此方法从分类帐中读取数据。它将输入“k”作为键，并返回与键“k”相关联的二进制值。

**2.putState(k，v):**

此方法将数据写入分类帐。它以“k”为键，以“v”为值。为了清楚起见，假设我们想要将 Alice 的年龄存储到分类帐中，我们可以将 **Alice** 作为键，将 **age** 作为值。

3 .删除状态(k):

此方法从分类帐中删除相关键“k”的值。

**4.getStateByRange(k1，k9):**

该方法返回分类帐中一组键的范围迭代器。它将遍历 startKey( k1)和 endKey(k9)并返回这两个键之间的所有键值。假设我们已经按照键 k1，k2，k3…k99 的顺序存储了一些用户数据，我们可以用这个方法简单地得到所有这些值的状态。

5.getTxID() :

此方法返回被调用事务的事务 Id。交易 Id 对于链上的每个交易都是唯一的。因此，事务 Id 在跟踪事务中起着至关重要的作用。

**6.getTxTimestamp():**

该方法返回创建事务时的时间戳。这是从交易通道头中提取的，因此它将指示客户端的时间戳，并且在所有背书者中具有相同的值。

## **编写你的第一个链码**

您已经学习了在 node.js 中编写链代码的一些先决条件。因此，现在，您可能会非常兴奋地编写您的第一个链代码，或者至少当我学习这些时是如此。

既然我们要在 Nodejs 中编写链码，我们首先需要创建传统的 npm 东西，比如 **package.json** 和 **index.js.** 如果你不知道这个 package.json 的话

a package.json

*   列出项目所依赖的包
*   使用[语义版本规则](https://docs.npmjs.com/about-semantic-versioning)指定您的项目可以使用的包版本
*   使您的构建可重复，因此更容易与其他开发人员共享

简而言之，我们的链代码依赖于 **fabric-contract-api** 和 **fabric-shim** 模块。我们在 package.json 中提到了这些包和版本。

我们还添加了**fabric-chaincode-node start**作为在对等体上安装 chain code 所需的启动脚本。

这是我们的 package.json:

```
 {
    "name": "Test-Chaincode",
    "version": "1.0.0",
    "description": "my first exciting node.js chaincode on Hyperledger-fabric",
   ** "main": "index.js",**
    "engines": {
        "node": ">=8",
        "npm": ">=5"
    },
    "scripts": {
        "lint": "eslint .",
        "pretest": "npm run lint",
        "test": "nyc mocha --recursive",
        **"start": "fabric-chaincode-node start"**
    },
    "engineStrict": true,
    "author": "Hyperledger",
    "license": "Apache-2.0",
    **"dependencies": {
        "fabric-contract-api": "~1.4.0",
        "fabric-shim": "~1.4.0"
    },**
    "devDependencies": {
        "chai": "^4.1.2",
        "eslint": "^4.19.1",
        "mocha": "^5.2.0",
        "nyc": "^12.0.2",
        "sinon": "^6.0.0",
        "sinon-chai": "^3.2.0"
    },
    "nyc": {
        "exclude": [
            "coverage/**",
            "test/**"
        ],
        "reporter": [
            "text-summary",
            "html"
        ],
        "all": true,
        "check-coverage": true,
        "statements": 100,
        "branches": 100,
        "functions": 100,
        "lines": 100
    }
}
```

如果你能仔细观察代码，有一行**“main”:“index . js”。**这是什么意思——在启动时(在安装链码期间), npm 模块用于检查 index.js 并在对等体上安装提到的契约。“所以我们的 index.js 包含作为模块导出的契约。

这是我们的 index.js 文件:

```
'use strict';
const testContract = require(’./logic’);
module.exports.contracts = [ testContract ];
```

**合同:**

*我们的商业逻辑是什么？？*

> 添加、检索和删除学生成绩。

1.添加标记包括将数据写入分类帐。所以我们将从 chaincode 存根接口使用 putState(k，v)方法。

2.检索标记包括从分类帐中读取数据。所以我们需要使用 getState(k)方法。

3.删除标记涉及删除数据。所以我们需要使用 deleteState(k)方法。

chaincode 从从 **fabric-contract-api** 模块引入范围键类 **Contract** 开始。这个类将用于编写逻辑。所有的链码函数都应该使用这个库类。

```
const { Contract}=require(’fabric-contract-api’);
 class testContract extends Contract {**//Functions go here**}
```

**添加标记**:

我们将创建一个 JavaScript 对象来存储学生在每个科目中的分数，并将该对象存储为值，将 **studentId** 存储为键。当通过服务器向数据库发送数据时，数据必须是字符串。因此，首先我们需要使用 **JSON.stringify()** 方法将这个 marks 对象转换成字符串，并应用 **buffer** 以二进制数据的形式发送到数据库。在这里，

```
async addMarks(ctx,studentId,subject1,subject2,subject3) { let marks={
     subj1:subject1, 
     subj2:subject2,
     subj3:subject3 
     }; 
await  ctx.stub.putState(studentId,Buffer.from(JSON.stringify(marks))); console.log(’Student Marks added To the ledger Succesfully..’); 
}
```

**删除标记**

```
async deleteMarks(ctx,studentId) {

 await ctx.stub.deleteState(studentId); 

console.log(’Student Marks deleted from the ledger Succesfully..’);

    }
```

**查询学生成绩:**

因为，我们把值以缓冲区的形式放在前面的 **addMarks()** 函数中。一旦被查询，它将返回缓冲区。因此，我们需要将 buffer 转换成 string，并解析成一个原始的 javascript 对象。在这里，

```
async queryMarks(ctx,studentId){

     let marksAsBytes = await ctx.stub.getState(studentId); 
     if (!marksAsBytes || marksAsBytes.toString().length <= 0) { 
       throw new Error(’Student with this Id does not exist: '); 
         } 
    let marks=JSON.parse(marksAsBytes.toString()); 
    return JSON.stringify(marks); 
   }
```

## 最终合同

你可以在这里找到完整的合同。

为了安装和测试这个契约，我将使用包含单个对等体的基本网络。在这个网络上，我们将在**peer0.org1.example.com**上安装一个名为 **mycc** 的 Node.js 链码，并在通道 **mychannel** 上实例化它。然后我们能够调用这些链码函数。确保 docker 安装在您的系统中。为了简单起见，我已经在 **chaincode/newcc** 目录中挂载了我们的 chaincode 文件(logic.js，index.js，package.json)。完整的代码可以在[这里](https://github.com/Salmandabbakuti/chaincode-essentials.git)找到

首先，我们需要启动网络，创建一个渠道。

```
git clone https://github.com/Salmandabbakuti/chaincode-essentials.gitcd chaincode-essentials/basic-network./start.sh
```

请稍等片刻。建立网络需要一些时间。如果您遇到任何权限错误，只需在 root 用户权限下运行即可。一旦我们的单对等网络建立并运行，我们就可以安装 chaincode 了。

为了安装和调用链接代码，我们可以使用对等体的 CLI 容器。进入 CLI 容器

```
docker exec -it cli bash
```

**安装并实例化链码**

```
peer chaincode install -n mycc -v 1.0 -p "/opt/gopath/src/github.com/newcc" -l "node"peer chaincode instantiate -o orderer.example.com:7050 -C mychannel -n mycc -l "node" -v 1.0 -c '{"Args":[]}'
```

**添加学生分数**

```
peer chaincode invoke -o orderer.example.com:7050 -C mychannel -n mycc -c '{"function":"addMarks","Args":["Alice","68","84","89"]}'
```

**查询学生“爱丽丝”的成绩**

```
peer chaincode query -o orderer.example.com:7050 -C mychannel -n mycc -c '{"function":"queryMarks","Args":["Alice"]}'
```

**从分类账中删除“爱丽丝”的标记**

```
peer chaincode invoke -o orderer.example.com:7050 -C mychannel -n mycc -c '{"function":"deleteMarks","Args":["Alice"]}'
```

上述脚本将从分类帐中删除关键字“Alice”和相关数据。如果您再次查询 Alice 的标记，您会得到一个错误消息，说“studentId 不存在”

我还制作了一个自动化脚本，用于在客户端目录中安装和测试这个链代码。按照以下步骤进行快速演示

首先，退出 CLI 容器，并在客户端目录中运行脚本

```
 exit # exits from CLI docker container if you're in
cd ..
cd client  #change your directory to clientchmod a+x start.sh./start.sh  #Automated script for testing
```

您也可以使用 **client/start.sh** 文件中定义的脚本手动调用链码函数。

# 结论

我们在这里演示了什么是链码，链码**存根**接口中的方法，链码的部署结构，以及编写链码和在网络上部署有多容易。我希望这篇文章能够帮助您开始编写链代码并在网络上部署。

**想知道我们如何在 Hyperldger Fabric 上构建最终用户应用吗？查看此处:**[**Hyperledger Fabric Node SDK 和客户端应用程序的演练**](/@Salmandabbakuti/walkthrough-of-hyperledger-fabric-client-application-aae5222bdfd3)

## 参考资料:

1.  Hyperledger 结构垫片 API:

[https://fabric-shim.github.io/release-1.4/index.html](https://fabric-shim.github.io/release-1.4/index.html)

2.Hyperledger 结构节点 SDK:

[https://fabric-sdk-node.github.io/master/index.html](https://fabric-sdk-node.github.io/master/index.html)

3.链码教程

[https://hyperledger-fabric . readthedocs . io/en/release-1.4/chain code . html](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode.html)

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   [开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   [NFT 十大市场造币集锦](https://coincodecap.com/nft-marketplaces)
*   [AscendEx Staking](https://coincodecap.com/ascendex-staking)|[Bot Ocean Review](https://coincodecap.com/bot-ocean-review)|[最佳比特币钱包](https://coincodecap.com/bitcoin-wallets-india)
*   [Bitget 回顾](https://coincodecap.com/bitget-review)|[Gemini vs BlockFi](https://coincodecap.com/gemini-vs-blockfi)|[OKEx 期货交易](https://coincodecap.com/okex-futures-trading)
*   [美国最佳加密交易机器人](https://coincodecap.com/crypto-trading-bots-in-the-us) | [经常性评论](https://coincodecap.com/changelly-review)
*   [在印度利用加密套利赚取被动收入](https://coincodecap.com/crypto-arbitrage-in-india)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [免费加密信号](/coinmonks/free-crypto-signals-48b25e61a8da) | [加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   杠杆代币的终极指南