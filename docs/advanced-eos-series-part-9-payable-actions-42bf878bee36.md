# 高级 EOS 系列—第 9 部分—应付行动

> 原文：<https://medium.com/coinmonks/advanced-eos-series-part-9-payable-actions-42bf878bee36?source=collection_archive---------2----------------------->

![](img/449524b9cc8d13375b0e26acca9a6f35.png)

本系列的目的是将您需要完成的作为 EOSIO 区块链分布式应用程序开发人员的技能的缺失部分汇集在一起。每篇文章都是按照难度排序的，所以如果你刚刚无意中来到这里并感到迷失，我建议你从第 1 部分开始，一路向上。这些例子的完整代码可以在 GitHub 上找到[。](https://github.com/MitchPierias/advanced-eos-examples/tree/master/09_Payable-Actions)

[](https://github.com/MitchPierias/advanced-eos-examples) [## MitchPierias/高级-EOS-示例

### 高级 EOS 合同示例集。所有合同现已更新，以符合最新的…

github.com](https://github.com/MitchPierias/advanced-eos-examples) 

到目前为止，您已经成功实现了智能合同的所有功能，但是您仍然缺少一个基本部分，即接收和转移价值…

## 让我们得到报酬

EOSIO 网络上的情况有所不同……付款是支付给`eosio.token`合同，而不是像其他区块链那样直接支付给合同的行为。这意味着我们需要监听对合同的支付，并提供适当的操作。别担心，这比听起来简单！我们将监听由`eosio.token`合同发出的`eosio.token::transfer`通知。通过用`[[eosio.token::on_notify(“eosio.token::transfer”)]]`替换常规的`[[eosio::action]`定义来定义我们的处理程序方法。当发生涉及您的合同帐户的令牌转移时，会发送此通知。让我们分解完整的传输通知处理程序。

```
[[eosio::on_notify("eosio.token::transfer")]] *void* purchase(name user, name receiver, asset value, string memo) { if (receiver != get_self() || caller == get_self()) return;

   eosio::symbol token_symbol("SYM", 0);
   eosio::check(value.amount > 0, "Insufficient value");
   eosio::check(value.symbol == token_symbol, "Illegal asset symbol");

   wallet_table balances(get_self(), get_self().value);
   *auto* wallet = balances.find(caller.value);

   if (wallet != balances.end()) {
     balances.modify(wallet, get_self(), [&](*auto* &*row*) {
       row.balance += value.amount;
     });
   } else {
     balances.emplace(get_self(), [&](*auto* &*row*) {
       row.account = caller;
       row.balance = value.amount;
     });
   }
}
```

你会注意到我们的方法看起来非常类似于一个常规的动作，那是因为它是一个动作！不同之处在于，这个动作是由`eosio.token`契约调用的`INLINE_ACTION`调用的，而不是由公共帐户直接调用的。

## 分解它

让我们拆开每一行，看看它在做什么。方法定义包括我们对`eosio.token::transfer`通知的订阅。我们需要处理通知中传递的所有参数。

```
[[eosio::on_notify("eosio.token::transfer")]] *void* purchase(name user, name receiver, asset value, string memo) {
  ... contract code ...
}
```

第一个参数是传输发送方的`eosio::name`值，第二个是接收方的 eosio::name 值。这应该是您在接收资金时的合同帐户名称，我们将在下一部分对此进行验证。最后，我们将转移值作为一个`eosio::asset`和一个备忘录`std::string`。

好了，现在在我们做任何事情之前，我们首先需要验证发送和接收参与者是我们所期望的。是时候验证了！

```
if (receiver != get_self() || caller == get_self()) return;
```

这一行是我们的理智检查，首先确保我们的契约`get_self()`是预期的接收者，其次，我们的契约本身没有调用这个传输。

```
eosio::symbol token_symbol("SYM", 0);
eosio::check(value.amount > 0, "Insufficient value");
eosio::check(value.symbol == token_symbol, "Illegal asset symbol");
```

我们将继续验证转移的资产，首先定义令牌`symbol`，然后检查大于零的发送值，它的`symbol`与我们期望的相匹配。你应该在这里做你喜欢的任何其他类型的验证。您可能希望检查当前时间是否在您的资助期内，或者呼叫者是否是注册的活跃用户。

现在我们已经验证了我们的论点，我们可以执行我们想要采取的行动。

```
wallet_table balances(get_self(), get_self().value);
*auto* wallet = balances.find(caller.value);
```

首先，我们将分配我们的`wallet_table`并搜索我们的`caller`。这一分配现在看起来类似于`multi_index`表上的前几节。

```
if (wallet != balances.end()) {
  balances.modify(wallet, get_self(), [&](*auto* &*row*) {
    row.balance += value.amount;
  });
} else {
  balances.emplace(get_self(), [&](*auto* &*row*) {
    row.account = caller;
    row.balance = value.amount;
  });
}
```

最后一段代码只是检查是否找到了钱包(用户之前的转账)，然后创建一个新条目或更新现有余额。

就是这样！如果您要部署到本地区块链，您还需要部署`eosio.token`合同，然后创建和发放令牌，然后才能执行向您的帐户转账。如果您需要与`eosio.token`合同交互的分步指南，请遵循[官方文档](https://developers.eos.io/eosio-home/docs/token-contract)。

## 排除故障

在处理转账和资产时，您可能会遇到令人讨厌的错误。我无意中遇到了`symbol precision mismatch`错误，当您试图使用不同于创建时指定的精度来发行或转移资产时，就会抛出这个错误。因此，如果我用`10000.00 SYS`创建我的资产，那么我需要使用两个十进制精度(如`12.00 SYS`而不是`12 SYS`)来发布和转移。

## 下一步是什么？

如果您希望在我分享本系列中的更多示例时得到通知，请务必单击 *follow* 按钮。或者关注我的 GitHub 个人资料，不要忘记鼓掌并开始表示感谢。

[](https://github.com/MitchPierias/advanced-eos-examples) [## MitchPierias/高级-EOS-示例

### 高级 EOS 合同示例集。所有合同现已更新，以符合最新的…

github.com](https://github.com/MitchPierias/advanced-eos-examples) 

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)