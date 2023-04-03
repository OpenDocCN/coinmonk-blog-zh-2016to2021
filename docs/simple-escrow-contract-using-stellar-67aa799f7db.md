# 使用 Stellar 的简单托管合同

> 原文：<https://medium.com/coinmonks/simple-escrow-contract-using-stellar-67aa799f7db?source=collection_archive---------2----------------------->

这篇文章是上一篇文章的后续，在这篇文章中，我尝试了一个简单的 XLM 交易:[创造星际智能合同的第一印象](/@slyg/first-impressions-at-creating-stellar-smart-contracts-d51f18552bbc)。

![](img/fda85cd345a366519e20f7c34243f582.png)

Émile-Antoine Bayard’s Illustrations for Around the Moon by Jules Verne (1870)

明星智能合同的一个常见例子是如何创建托管。这就是我将在下面描述的。我很清楚[stellar.org](https://www.stellar.org/)网站上的例子也解释得很清楚，我在这里的重点是用一种新手语言给出另一个新手介绍。

下面是我们将遵循的场景，我们希望(A)虱子和(B)ob 的签名都能从托管账户中释放资金:

*   Alice 创建并资助一个托管账户
*   Alice 创建了一个到 Bob 账户的托管支付交易
*   稍后，爱丽丝签署托管支付交易
*   最后，Bob 签署付款交易，并将其提交以接收资金

我们会认为爱丽丝和鲍勃的账户已经存在于恒星网络中。您可以参考我上一篇文章来了解如何使用 Stellar testnet 来实现这一点。

请原谅，这些步骤可以简化很多。但我选择将它们分开，希望能让推理更清晰。

## 爱丽丝创建了一个托管账户

Alice 使用她的账户创建一个新账户(托管账户)并为其提供资金。选择 2.5 流明是因为恒星账户需要至少 0.5 XLM 的余额，正如文件中规定的[。我们以后会看到为什么增加了 2 流明。](https://www.stellar.org/developers/guides/concepts/fees.html#minimum-account-balance)

一旦建立，托管账户创建交易被提交到网络。

## 托管账户配置(multisig)

我们想限制代管账户的支出，因为爱丽丝和鲍勃都需要签名。这是通过更改托管帐户的属性来完成的。

这是需要意识到的一个关键点:您需要在托管账户上设置选项来应用这些约束。

在`threshold`属性中，通过给`masterWeight`一个 0 值，我们防止托管账户私钥在将来被用来签署任何交易。

您会看到签名者的权重被设置为每一个`1`。因此执行任何交易所需的[阈值](https://www.stellar.org/developers/guides/concepts/operations.html#thresholds)被设置为`2`:一个给爱丽丝，一个给鲍勃(零个给托管)。

因此，一旦该修改被提交到 Stellar network，Bob 和 Alice 都需要签署来自托管账户的交易。

这个修改也是有效的，因为托管账户已经有足够的流明(2.5):每个签名者 1+0.5 最小余额。

## 爱丽丝将流明发送到托管账户

这可能是在 Alice 创建托管帐户时完成的。然而，为了清楚起见，我将这个事务隔离开来。

## 创建托管支出交易

该交易仅指定从托管账户向 Bob 付款。

对于双方签署的交易，并触发支付给鲍勃，它必须有可能发送过来。但是 Stellar 网络只验证所有签署者签署的交易。

创建一个事务并不意味着它必须提交给网络。毕竟，当您使用 Stellar JS SDK 时，在您最终通过 SDK 的`submitTransaction()`方法提交事务之前，事务只是内存中的 JS 对象。

幸运的是，Stellar 使用一种你可以用来交换交易的格式: [XDR](https://www.stellar.org/developers/guides/concepts/xdr.html) 。这就是为什么下面的脚本可以*序列化*该事务并将其保存为文件的原因。

在文档的大多数例子中，你会看到 Alice 和 Bob 的签名在同一个脚本中执行。但是在现实世界的用例中，你很难同时访问 Alice 和 Bob 的私钥，或者甚至在同一个设备上访问……如果是这样，你可能会遇到大问题。

*稍后……*

## **爱丽丝签署**托管支付**交易**

Alice 可以对事务进行反序列化，并使用其帐户的私钥对其进行签名，然后再次进行序列化，这使得事务到目前为止是半签名的。

## Bob 签署并提交交易

现在，Bob 的私钥也可以用于签署交易，并且被所有签署者签署后，可以提交给网络。

这就对了:在接下来的 5 秒钟内，Bob 的账户将会被存入资金。

我希望这个演示将帮助您从自己的用例开始，并揭示 Stellar 如何成为智能合同实现的一个非常好的赌注。

你还会发现我在这个频道发布的几个视频:[https://www.youtube.com/channel/UCAKSRclAwfHyN6dWibj4iVw](https://www.youtube.com/channel/UCAKSRclAwfHyN6dWibj4iVw)

*更新(20/08/2019):更新 sdk 更新后的代码示例，添加视频链接*

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)