# 如何创建 EOS dapp(简单指南)

> 原文：<https://medium.com/coinmonks/how-to-create-an-eos-dapp-simple-guide-ddc1e9d05cb2?source=collection_archive---------1----------------------->

![](img/113dcb923444d0fb6a8352f294b2613f.png)

两周前，我在香港参加了首届全球 EOS 黑客马拉松。我们使用 ReactJS 和 EOSJS 构建了一个点对点数据市场分布式应用程序(dapp ),外加一个用 C++编写的自定义智能契约。虽然我们没有获胜(获胜者使用了视网膜扫描硬件和 EOS 内置的身份验证功能)，但我们的项目得到了 EOS 社区的大量认可。

这是一个循序渐进的指南，适合任何想要开始构建 EOS dapp 的人。

首先，我强烈推荐使用 Docker 并安装 EOSIO Dev 映像。请点击此处查看 EOSIO 官方文档。[https://developers . EOS . io/EOS io-nodeos/docs/docker-quick start](https://developers.eos.io/eosio-nodeos/docs/docker-quickstart)

# 创建钱包

为了构建一个能够真正与钱包和账户交互的 dapp，我们需要对它们进行设置。

在终端中，键入`cleos wallet create`

这将返回一个密码。请将它保存在安全的地方，因为你很快会再次需要它。

*注意:如果这不起作用，您没有在上面的步骤 1 中为您的“cleos”起别名。确保您遵循 EOSIO 提供的全部 docker 快速入门指南。*

如果你遇到钱包被锁的问题，只需输入`cleos wallet unlock`

然后输入您之前保存的密码。

# 创建帐户

为了与 dapps 互动，您需要在钱包中有一个帐户。让我们开始建立一个帐户。我们首先将两套钥匙放入我们的钱包。一个是 OwnerKey，一个是 ActiveKey。确保您保存了两个密钥对。车主钥匙应始终保持高度安全。

## 创建您自己的密钥

在你的终端中，输入`cleos create key`

终端将返回一个私钥和一个公钥。

接下来，键入`cleos wallet import [Private Key from above]`

终端将为[来自上面的公钥]返回导入的私钥

## 创建您的 ActiveKey(与创建 OwnerKey 的步骤相同)

在你的终端中，输入`cleos create key`

终端将返回一个私钥和一个公钥。

接下来，键入`cleos wallet import [Private Key from above]`

终端将为[来自上面的公钥]返回导入的私钥

## 创建您的第一个帐户

在您的终端中，键入

`cleos create account eosio user1 [Owner Public Key] [Active Public Key]`

创建完第一个帐户后，您可以按照相同的步骤创建第二个帐户(例如，如果您的应用程序将让用户互相发送令牌)。我假设您创建了名为 user2 的第二个帐户。

# 创建令牌(EOSIO 让这变得非常简单！)

首先，我们将创建一个令牌。与以太坊不同，使用 EOS 创建令牌生态系统非常简单，可以从终端完成。我们将部署 EOS 附带的标准令牌，但是您可以轻松地修改和定制令牌合约，或者从头开始构建自己的令牌合约。

首先创建一个帐户，我们将向其部署令牌合约。按照创建帐户(如上)中的步骤生成 OwnerKey 和 ActiveKey。

然后，在你的终端

```
cleos create account eosio eosio.token [New Owner public key] [New Active public key]
```

接下来，我们将把标准令牌合约部署到这个新创建的帐户中。

```
cleos set contract eosio.token build/contracts/eosio.token -p eosio.token
```

请注意，在-p eosio.token 上方的行中，表示帐户 eosio.token 正在授予部署契约的权限。您的钱包可能会被锁定，在这种情况下，您需要按照创建钱包(如上)中的解锁步骤解锁钱包。当您创建您自己的定制契约时，您将使用与我们在这里相同的方式来部署它们，但是您将使用您的定制契约来代替`cleos set contract eosio.token`。

接下来，我们将使用您的代币合约创建代币。您可以随意命名令牌，并设置颁发的令牌总数。

```
cleos push action eosio.token create ‘[ “eosio”, “1000000000.0000 TOK”]’ -p eosio.token
```

上面的示例创建了 10 亿个 TOK 令牌，发行者为“eosio”。最后一步是向我们的一个账户发放代币。请注意，“备忘录”是一种将字符串描述附加到交易的方式。此外，我们现在从 eosio 而不是 eosio.token 获得许可(通过-p ),因为 eosio 帐户持有所有 10 亿个令牌。

```
cleos push action eosio.token issue '[ "user1", "100.0000 TOK", "memo" ]' -p eosio
```

就这样，您现在已经有了可以插入到您的应用生态系统中的令牌！

# 使用 EOSJS 将 EOS 连接到您的 dapp

对于一个实际的应用程序，您可能想要创建一个自定义契约。但是我们现在已经有足够的东西来制作一个 MVP 了，所以接下来我将向您展示如何在您的应用程序中与 EOS 区块链进行交互。

如果您还没有设置 react 应用程序，请键入

`create-react-app [name of your react app]`

在这个基本目录中，在您的终端中键入以下内容来安装 eosjs 并将其添加到您的 package.json 中

`npm install eosjs -s`

然后，在您的 App.js 文件中，您需要使用以下内容导入 eosjs:

`import Eos from “eosjs”;`

我们必须初始化 eosjs 的一个实例，以便在我们的应用程序中使用它。为此，请将以下内容添加到全局范围中。注意`user1ActivePrivKey`和`user2ActivePrivKey`是我们在创建账户时创建的私钥。不要在这里使用所有者密钥；主动键更安全。您应该将这些键保存为环境变量，如果您打算在任何时候公开这些代码，就应该导入它们。

```
const eos = Eos({
  keyProvider: [
    user1ActivePrivKey,
    user2ActivePrivKey
  ]
});
```

现在，我们可以轻松地与本地 EOS 节点进行交互。我们的第一步是检查我们两个用户帐户的令牌余额。

```
eos.getCurrencyBalance(“eosio.token”, 'user1').then(tokenBalance =>     {
  console.log(tokenBalance)
});
```

由于我们在上一节中向用户 1 发放了令牌，您应该看到当前的令牌余额为 100.0000 TOK。如果我们检查 user2 的令牌余额，您应该看不到任何余额。

```
eos.getCurrencyBalance(“eosio.token”, 'user2').then(tokenBalance =>    {
  console.log(tokenBalance)
});
```

在这个例子 dapp 中，我们将有一些非常基本的功能。我们假设用户 1 正在接收某个值，作为交换，向用户 2 发送令牌。在 eosjs 中，最好的方式是通过“交易”。在这里，我们将发送 5 TOK

```
eos
  .transaction({
    actions: [
      {
        account: "eosio.token",
        name: "transfer",
        authorization: [
          {
            actor: "user1",
            permission: "active"
          }
        ],
        data: {
          from: "user1",
          to: "user2",
          quantity: `5.0000 TOK`,
          memo: "some description of the transaction"
        }
      }
    ]
  })
  .then(result => {
    // Check token balances again.  You'll now see 5 TOK in user2’s
    // account and 95 TOK in user1’s account
  });
```

当您构建一个自定义契约时，您将与它进行交互，就像与令牌契约进行交互一样。

# 后续步骤

有关 EOSIO 团队的其他示例以及为您的 dapp 构建其他功能，请访问[官方 EOS 文档](https://developers.eos.io/eosio-cpp/docs)。

有问题吗？问题？请留下你的评论，我会和你一起努力。如果你觉得这个指南很有帮助，请鼓掌几下，帮助其他有抱负的 EOS 开发者找到它。另外，如果你对高级指南感兴趣，请告诉我。我们为黑客马拉松建立了更复杂的东西，我很乐意分享更多！

如果你想了解更多我在黑客马拉松的经历，[点击这里查看我的另一篇文章](/@dliebeskind/my-eos-hackathon-experience-hong-kong-557547c7de84)！

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   [币安 vs 比特邮票](https://coincodecap.com/binance-vs-bitstamp) | [比特熊猫 vs 比特币基地 vs Coinsbit](https://coincodecap.com/bitpanda-coinbase-coinsbit)
*   [如何购买 Ripple (XRP)](https://coincodecap.com/buy-ripple-india) | [非洲最好的加密交易所](https://coincodecap.com/crypto-exchange-africa)
*   [非洲最佳密码交易所](https://coincodecap.com/crypto-exchange-africa) | [胡交易所评论](https://coincodecap.com/hoo-exchange-review)
*   [eToro vs robin hood](https://coincodecap.com/etoro-robinhood)|[MoonXBT vs Bybit vs Bityard](https://coincodecap.com/bybit-bityard-moonxbt)
*   [面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [免费加密信号](/coinmonks/free-crypto-signals-48b25e61a8da) | [加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   杠杆代币的终极指南