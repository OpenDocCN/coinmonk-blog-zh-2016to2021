# 如何花光闪电通道的所有资金

> 原文：<https://medium.com/coinmonks/how-to-spend-all-funds-from-a-lightning-channel-44eded09a6dc?source=collection_archive---------5----------------------->

## 该协议建议总是保持一些保留，但是如果各方相互信任，则可能没有必要

![](img/a51cb90cc180bfebbfe89734badb048b.png)

## 为什么不能花光所有的资金？

[闪电网络规范将](https://github.com/lightningnetwork/lightning-rfc/blob/master/02-peer-protocol.md#the-open_channel-message) `channel_reserve_satoshis`定义为另一个节点作为直接支付要保留的最小金额。还建议预留值为渠道总值的 [1%。“最初，这一储备可能无法满足，因为只有一方有资金；但该协议确保在满足这一储备方面始终有进展，一旦满足，就保持下去。”](https://github.com/lightningnetwork/lightning-rfc/blob/master/02-peer-protocol.md#rationale)

这一储备作为防范可能的盗窃企图的保险。如果一个对等体试图通过广播旧的信道状态来欺骗，其交易对手可以提交[惩罚交易](https://github.com/lightningnetwork/lightning-rfc/blob/master/00-introduction.md)。该事务从行为不当的节点拿走通道上的所有资金作为惩罚。并且`channel_reserve_satoshis`确保每个对等体倾向于在信道上有一些保留，至少 1%。这样，在博弈论中，每个参与者都有诚实行为的动机。因为如果试图作弊，每个人都会有所损失。

虽然这可能有点奇怪，但它是有逻辑的。但在某些情况下，这种矜持可能会比较讨厌。

当我第一次测试闪电支付时，我只是从一个对等点向另一个对等点发送了 1 satoshi。当我意识到我不能把同一个聪送回去时，我很惊讶。因为即使对等体在频道上已经有 1 个 satoshi，它也低于`channel_reserve_satoshis`限制，可消费金额仍然为零。

为了演示，除了我的主节点之外，我将启动另一个闪电节点[。然后我会资助他们之间的一个通道，送 1 个 satoshi。然后检查是否因为储备限制而无法使用。之后，我在对等体的数据库上重写`channel_reserve_satoshis`，并将那个 satoshi 发送回去。](/coinmonks/my-lightning-node-setup-with-c-lightning-45bbb9993c0)

# 技术细节

![](img/16699e413a4e4850b83017ce524493c3.png)

## 第一步:测试节点设置

## 第二步:向测试节点发送付款

## 第三步:重新配置信道预留

## 第四步:把聪放回去

# 结束语

闪电网络仍处于测试阶段，它可能包含一些错误。

我上面描述的绝对不是一个常见的用例，没有经过大量的测试。也许这根本不是一个用例。我认为为此重新配置一个对等体本身是不够的。同伴们可能想强迫他们的伙伴保留一些储备。一个同行可能应该请求其交易对手的许可来使用所有资金。

我只在 c-lightning v0.6 实现之间测试了这一点。其他版本的行为可能有所不同。

耐心点！不计后果！