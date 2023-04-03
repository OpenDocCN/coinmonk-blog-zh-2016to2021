# 如何打造一款出众的钱包？

> 原文：<https://medium.com/coinmonks/how-to-create-a-stellar-wallet-f29e7220908?source=collection_archive---------9----------------------->

为了创造一个一流的钱包，我们需要 StenciljS 工具链。`Stenciljs`提供了最好的框架之一，它是现代化的前端，并完全基于标准化的 web 组件将所有内容快速有效地放置到其位置，这些组件适用于每种 web 浏览器。它有助于创建一种方便的方法来制作 web 应用程序，允许您从头到尾查看制作一个出色钱包的所有入口和出口。

![](img/4333ec87f6b56f8c2a8be37a65b83113.png)

## **第一步——建立项目**

我们将从项目设置开始。为此，我们将打开一个终端并开始初始化一个新项目。选择项目类型时，屏幕上会出现提示。继续按照模块化组件选择组件。那就运行它。这将初始化组件生成的脚本。然后是恒星钱包。使用 SCSS 而不是 CSS 来设计样式。安装完样式包后，转到 stencil.config.ts 并对其进行修改。然后保存所有样式文件，然后更新 wallet.tsx。

## **步骤 2——弹出模式创建**

为了创建弹出模式和新组件，将实现浏览器的提示功能。从生成一个新组件开始。给它命名为恒星提示器。改变。打开 src/components/prompt/后 css 文件到. scss .写入特定的样式文件。之后，提示的内容。必须更换 tsx。在我们前进之前，我们必须确保我们进口了洛达什-es。

## **步骤 3 —创建主要账户类别**

Stellar Account 可以描述为允许包含公钥的类。我们将从设置账户状态和 [Stellar](https://www.leewayhertz.com/what-is-stellar-blockchain/) 账户类开始。将对提示类和提示状态进行同样的操作。这样做之后，导入的事件和方法的赋值就必须完成了。在呈现组件之前，componentWillLoad 会预填充值 props 和 state。接下来，在创建两个文件后添加到 src/services 目录，然后 error.ts 将保留，这是 API 请求过程中的一个正常错误处理程序。

![](img/14e9b21ce9d09d2ba71ccb51f1baa349.png)

## **步骤 4 —密钥存储设置**

从修改 storage.ts 开始，然后安装并开始设置新包为[@电容器/内核](http://twitter.com/capacitor/core)。初始化电容器项目，然后电容器项目就可以启动了。

## **步骤 5 —事件处理设置**

继续设置事件处理。这个过程就是渲染一个法线。基于一系列条件值呈现 DOM 的 tsx 文件。

## **步骤 6 —方法创建**

英寸/methods/createAccount.ts 文件:

```
import sjcl from “[@tinyanvil/sjcl](http://twitter.com/tinyanvil/sjcl)”;import { Keypair } from “stellar-sdk”;import { handleError } from “[@services/error](http://twitter.com/services/error)”;import { set } from “[@services/storage](http://twitter.com/services/storage)”;export default async function createAccount(e: Event) {try {e.preventDefault();const pincode_1 = await this.setPrompt(“Enter a keystore pincode”);const pincode_2 = await this.setPrompt(“Enter keystore pincode again”);if (!pincode_1 || !pincode_2 || pincode_1 !== pincode_2)throw “Invalid pincode”;this.error = null;const keypair = Keypair.random();this.account = {publicKey: keypair.publicKey(),keystore: sjcl.encrypt(pincode_1, keypair.secret(), {adata: JSON.stringify({publicKey: keypair.publicKey(),}),}),};await set(“keyStore”, btoa(this.account.keystore));} catch (err) {this.error = handleError(err);}}
```

## **第 7 步—账户创建**

以上所有步骤都是为了触发一个提示模式，该模式将请求一个 pin 码。现在将最终创建一个帐户。

## **步骤 8——复制地址和密码**

我们现在将复制地址和密码，然后退出并设置提示。

英寸/methods/copyAddress.ts:

```
import copy from “copy-to-clipboard”;export default async function copyAddress(e: Event) {e.preventDefault();copy(this.account.publicKey);}
```

npm 身份证复制到剪贴板

## **第九步——退出并设置提示**

我们将最终发出退出的命令，

。/方法/注销. ts

最后，将在 wallet.ts 文件中设置提示

是。/methods/setPrompt.ts

恒星区块链发展凭借其内置的秩序册、独特的共识协议以及与现有金融基础设施的联系，正在迅速崛起。它具有多种优势，如快速交易、国际范围的接触、全球范围的资产交换等。因此，Stellar 的目标是成为标准化方法的缩影和区块链的资金转移平台。

> 加入 [Coinmonks 电报频道](https://t.me/coincodecap)，了解加密交易和投资

## 另外，阅读

*   [印度的加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9) | [比特币储蓄账户](/coinmonks/bitcoin-savings-account-e65b13f92451) | [HitBTC 评论](/coinmonks/hitbtc-review-c5143c5d53c2)
*   [币安收费](/coinmonks/binance-fees-8588ec17965) | [Botcrypto 审查](/coinmonks/botcrypto-review-2021-build-your-own-trading-bot-coincodecap-6b8332d736c7) | [Hotbit 审查](/coinmonks/hotbit-review-cd5bec41dafb) | [KuCoin 审查](https://blog.coincodecap.com/kucoin-review)
*   [我的加密副本交易经历](/coinmonks/my-experience-with-crypto-copy-trading-d6feb2ce3ac5) | [AAX 交易所评论](/coinmonks/aax-exchange-review-2021-67c5ea09330c)
*   [逐位融资融券交易](/coinmonks/bybit-margin-trading-e5071676244e) | [币安融资融券交易](/coinmonks/binance-margin-trading-c9eb5e9d2116) | [超位审核](/coinmonks/overbit-review-9446ed4f2188)
*   [加密货币储蓄账户](/coinmonks/cryptocurrency-savings-accounts-be3bc0feffbf) | [YoBit 审核](/coinmonks/yobit-review-175464162c62) | [Bitbns 审核](/coinmonks/bitbns-review-38256a07e161)
*   [Botsfolio vs nap bots vs Mudrex](/coinmonks/botsfolio-vs-napbots-vs-mudrex-c81344970c02)|[gate . io 交流回顾](/coinmonks/gate-io-exchange-review-61bf87b7078f)