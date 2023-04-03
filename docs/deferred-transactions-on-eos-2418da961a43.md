# EOS 上的延期交易

> 原文：<https://medium.com/coinmonks/deferred-transactions-on-eos-2418da961a43?source=collection_archive---------6----------------------->

![](img/7015db2f1d1dc3a07094b83e3f9af14f.png)

EOS 有两种方式发送动作，*内嵌*和*延迟*。内联动作可以直观地被视为同步执行，而延迟动作则被视为异步代码。**内嵌动作**可以从一个契约中派生出来，然后在与原始动作相同的事务中执行。它们是可靠的，因为它们被保证执行，并且它们可以使整个事务失败，恢复同一事务的先前动作的任何改变。(事务是在同一个块中执行的动作的列表。)

**延期行动**是由合同安排在未来某个时间执行的行动。*内联*动作的最大区别是它们甚至不能保证运行。该事务也可以被节点丢弃。同样，如果它们失败，调度延迟动作的事务是*而不是*恢复的。事实上，没有办法恢复它们，在执行延迟事务时，它们已经应用于前一个块中的区块链。延迟的事务也可以从代码中再次取消。

> 推迟的动作被安排在最好的情况下，在稍后的时间运行，由生产者决定。不能保证延迟的操作会被执行。 [EOS 开发门户](https://developers.eos.io/eosio-cpp/docs/communication-model#section-deferred-communication)

由于延期交易的巨大缺点，人们可能会怀疑是否应该使用延期交易。延期交易肯定有其存在的权利。除了将来以编程方式安排一个动作的明显原因之外，它们还可以用于**任何非必要的事情**。EOSIO 软件仅提供一定量的毫秒(30ms)来执行事务。注意，这个限制适用于*事务*，它的动作列表和产生的内联动作。延迟事务提供了一种绕过这种限制的方法，因为它们是在新事务中运行的。非必要的动作，如登录，或者可以由用户在不执行时再次手动触发的动作，如在跟踪余额的赌博合同中提取资金，可以被实现为延迟动作。

> 每个交易必须在 30 毫秒或更短时间内执行。如果一个事务包含多个操作，并且这些操作的总和大于 30 毫秒，则整个事务将失败。在对它们的操作没有并发性要求的情况下，这可以通过将消耗 CPU 的操作包含在单独的事务中来规避。 [EOS 开发门户](https://developers.eos.io/eosio-cpp/docs/communication-model#section-transaction-limitations)

以下是如何在 EOS 中创建延期交易:

```
#include <eosiolib/transaction.hpp> // include this for transactions

class deferred_example : public eosio::contract
{
  public:
    using contract::contract;

    // this action will be called by the deferred transaction
    ACTION deferred(name from, const string &message)
    {
        require_auth(from);
        print("Printing deferred ", from, message);
    }

    ACTION send(name from, const string &message, uint64_t delay)
    {
        require_auth(from);

        eosio::transaction t{};
        // always double check the action name as it will fail silently
        // in the deferred transaction
        t.actions.emplace_back(
            // when sending to _self a different authorization can be used
            // otherwise _self must be used
            permission_level(from, "active"_n),
            // account the action should be send to
            _self,
            // action to invoke
            "deferred"_n,
            // arguments for the action
            std::make_tuple(from, message));

        // set delay in seconds
        t.delay_sec = delay;

        // first argument is a unique sender id
        // second argument is account paying for RAM
        // third argument can specify whether an in-flight transaction
        // with this senderId should be replaced
        // if set to false and this senderId already exists
        // this action will fail
        t.send(now(), from /*, false */);

        print("Scheduled with a delay of ", delay);
    }
};

EOSIO_DISPATCH(deferred_example, (send)(deferred))
```

该代码类似于发送内联操作的代码。不同之处在于，我们需要先创建一个新的空事务。发送交易时，需要指定*发送方 id* 。此 id 在您的合同发送的所有延期交易中必须是唯一的。它可用于识别对错误处理有用的事务，并在取消仍在*进行中的*即尚未执行的延迟事务时需要该事务。如果无法从您的合同上下文中创建唯一的发送方 id，您可以为您的延期交易 id 创建一个计数器，并将其存储在合同中的一个`eosio::singleton`中。调度延迟事务的工作原理是将事务“存放”在区块链上，这需要事务大小的 RAM。可以指定付款人，并要求其授权。

要取消与`sender_id`的正在进行的交易，只需调用`cancel_deferred(sender_id)`。

当延期交易失败时，`eosio`向您的合同发送一个`onerror`动作，带有`sender_id`和交易数据:

```
class deferred_example : public eosio::contract
{
    // ...

    void onError(const onerror &error)
    {
        // this function should have a counter to not retry forever
        print("Resending Transaction: ", error.sender_id);
        transaction dtrx = error.unpack_sent_trx();
        dtrx.delay_sec = 3;
        dtrx.send(now(), _self);
    }

    // ...
};

extern "C" void apply(uint64_t receiver, uint64_t code, uint64_t action)
{
    if (code == "eosio"_n.value && action == "onerror"_n.value)
    {
        eosio::execute_action(eosio::name(receiver), eosio::name(code), &deferred_example::onError);
    }
    // ...
}
```

延迟事务导致*防御性编程*技术。您永远无法确定该操作是否被实际执行，因此您的契约需要在其表中表示当前状态，并重试该操作。如果延迟的事务会影响用户，比如发送资金，您应该总是有一个单独的操作，以非延迟的方式实现逻辑，并且可以由用户手动触发，比如取款操作。必要的核心逻辑应该由初始操作或内联操作来处理。

延迟事务在安排它的契约的许可下运行。这个契约还为产生的动作支付 CPU /网络带宽成本。

> 延期交易具有发送它们的合同的权威。 [EOS 开发门户](https://developers.eos.io/eosio-cpp/docs/communication-model#section-deferred-communication)

请注意，延期交易只能提前 45 天安排。但是，您可以通过让延迟操作重复调度新的延迟事务，直到达到您想要的时间跨度，来解决这个问题。同样，这很容易失败，应该尽可能避免。

下面是 EOS 主网上关于事务的一些其他有趣的[配置设置](https://github.com/CryptoLions/EOS-MainNet/blob/master/genesis.json)。

```
{
  "initial_configuration": {
    // ...
    // the maximum billable cpu usage (in microseconds) that the chain will allow regardless of account limits
    "max_transaction_cpu_usage": 150000,
    // the minimum billable cpu usage (in microseconds) that the chain requires
    "min_transaction_cpu_usage": 100,
    // the maximum number of seconds that an input transaction's expiration can be ahead of the time of the block in which it is first included
    "max_transaction_lifetime": 3600,
    // the number of seconds _after_ the time a deferred transaction can first execute until it expires
    "deferred_trx_expiration_window": 600,
    // the maximum number of seconds that can be imposed as a delay requirement by authorization checks
    // 45 days
    "max_transaction_delay": 3888000,
    "max_inline_action_size": 4096,
    "max_inline_action_depth": 4,
    "max_authority_depth": 6
  }
}
```

这意味着我们可以提前 45 天安排事务，节点在事务到期前有 10 分钟时间执行事务。不幸的是，如果一个事务过期并且从未执行过，那么在您的合同的`onerror`回调中不会通知您没有执行。这个`onerror`动作只有在事务首先被执行，但由于断言错误而失败时才被调用。

如果你想更多地了解 EOS 的沟通模式，[这篇文章](https://developers.eos.io/eosio-cpp/docs/communication-model)很有帮助。

[![](img/e69b8309c521ad6eb94c726beee8a1a6.png)](https://learneos.one#modal)

最初发表于 [cmichel.io](https://cmichel.io/deferred-transactions-on-eos/)

![](img/449450761cd76f44f9ae574333f9e9af.png)

[👆 **Click to read today’s top story** 👆](http://bit.ly/2G71Sp7)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)