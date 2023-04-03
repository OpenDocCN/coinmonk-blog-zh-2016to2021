# Qubic:基于群体的计算—由 IOTA 提供支持—第 1 部分

> 原文：<https://medium.com/coinmonks/qubic-quorum-based-computations-powered-by-iota-3770fbd62341?source=collection_archive---------2----------------------->

**第一部分-简介**

# 什么是 Qubic？

简而言之:Qubic 最初是一个由 initalism 转变而来的缩写词，QBC，代表基于法定人数的计算。

[维基百科上的法定人数(分布式计算)](https://en.wikipedia.org/wiki/Quorum_(distributed_computing))

具体来说，Qubic 是一种协议，它指定了 IOTA 针对基于法定人数的计算的解决方案，包括 oracle 机器、外包计算和智能合同等构造。Qubic 在 Tangle 上提供通用的、基于云或雾的、无权限的多处理能力。从长远来看，Qubic 将允许人们利用全球未使用的计算能力来满足无数的计算需求，同时帮助保护 IOTA Tangle:基于 IOTA 的世界超级计算机。

更一般地说，量子比特是一种我们称之为打包的基于法定人数的计算，它根据量子比特协议发生。下面是一些不同类型的量子比特的例子——虽然为了清楚起见，这里对它们进行了区分，但它们都是一个单一的通用概念的变体:基于群体的计算，或量子比特。

# ORACLE 机器

![](img/06ede9a88ad0f9ae7ad47893b58982a5.png)

当处理涉及像 Tangle 这样的分布式数据库的计算时，很难获得来自现实世界的数据，或者来自执行环境之外的数据。

qubic 通过 oracle 机器访问外部数据，Oracle 机器充当 qu BIC 和外部世界之间的透镜。该协议允许读取和传输数据，同时保持所讨论的数据的一致性的高度确定性。此外，虽然这在技术上意味着先知本身(例如，诸如用户或提供传感器数据的机器等外部参与者)不在协议的范围内，但是 Qubic 仍然可以通过法定人数提供关于所讨论的数据的正确性的高度确定性。

Qubic 以这种方式使用 oracle(因此也使用 Oracle 机器)来提供与基于群体的计算任务相关的输入数据。这种输入数据的一些例子是:

*   来自真实传感器的温度数据
*   来自股票市场的当前或历史股票价值数据
*   个人属性，如当前年龄、婚姻状况、死亡状况
*   选举结果

# 外包计算

![](img/eae5dc102837a9e6ad5b65d41d5111d1.png)

对于任何计算设备来说，总会有一些任务对该设备来说过于资源密集而无法计算，或者一些任务需要超出本地可用数据的数据。对于组成物联网(IoT)的设备来说尤其如此。这些设备通常受限于缺乏存储器、处理能力、能量可用性或上述所有因素。如果这种低功耗设备可以简单地将密集计算外包给外部更强大的机器会怎么样？

Qubic 实现了这种外包计算，并允许消费者和生产者安全、无许可地参与。该协议允许任何人在一个或多个外部设备上创建或请求运行计算任务，这些外部设备反过来将结果传输回请求者。同样，任何人都可以找到任务并参与处理它们。

与 oracle 机器一样，这种处理是以分散和安全的方式进行的，Qubic 协议确保结果具有高度的可信度。

# 智能合同

![](img/482c391d22f48af8e35ee60c192e6add.png)

一般来说，智能合同通过将合同义务封装在软件中，自动验证和强制执行，从而消除了第三方强制执行的需要。任何有权访问该合同的人都可以证明特定的事件总是会导致特定的结果。虽然预计智能合约在未来可能会用于取代许多种类的现实世界的纸质合约，但迄今为止，智能合约最常见的用例是创建虚拟令牌，这些令牌继承了创建它们的分布式系统的许多属性。

虽然 Qubic 当然能够支持这些类型的传统智能合约，但免费交易与通用法定人数计算的结合为全新的可能性打开了大门。例如，一个智能契约可以用来将来自不同预言的温度数据聚集成一个平均温度，这个平均温度会定期发布给 Tangle。智能合约现在已经成为一个 oracle 本身，也就是说，合约本身已经成为一个外部数据源，可供 oracle 机器拾取并发送回其他 qubic。

这些类型的智能合约也只是基于法定人数的计算的更一般能力的迭代。因此，Qubic 还提供了一种标准化的方法来定义、验证和执行智能合同的结果，并具有很高的确定性。

# 把所有的放在一起

正如 Qubic [预告视频](https://qubic.iota.org/static/teaser_720p.mp4)中所想象的那样，这里有一组 Qubic(具体来说:一台甲骨文机器、一个外包计算和一份智能合同)，它们一起工作，并随着时间的推移进化，以做一些有用的事情——在这种情况下，就是外汇交易。

1.  **汇率 qubic** :作为一台原始的甲骨文机器，定期发布泰戈上的汇率。数据始发者可以是预定义的，也可以不是。在这种情况下，神谕从 Tangle 环境之外获取数据，这是不可能通过其他方式获得的。
2.  **外汇预测 qubic** :获取由汇率 qubic 提供的数据，并预测近期的汇率。在这种情况下，oracles 执行密集的外包计算，这对于低级设备来说太困难或太昂贵了。
3.  **投资组合管理 qubic** :获取上述两个 qubic 提供的数据，用挂钩的虚拟欧元卖出或买入挂钩的虚拟美元。在这种情况下，oracles 执行一个智能合约，允许所有者不必手动处理所有交易。

([链接至第二部分](/@Zass27/qubic-quorum-based-computations-powered-by-iota-52e13c46bdde))

[![](img/aecb5d9eef0a0c1c7471064043e7eb6a.png)](https://medium.com/coinmonks/iota/home)

**Click to learn more about IOTA**