# 用向量和矩阵表示神经网络

> 原文：<https://medium.com/coinmonks/representing-neural-network-with-vectors-and-matrices-c6b0e64db9fb?source=collection_archive---------0----------------------->

在之前的几篇博客中，我们学习了神经元是如何工作的，并创建了一个简单的神经网络实现，它几乎可以解决一个简单的线性方程。

*   [神经元的功能](http://www.tech-quantum.com/function-of-the-neuron/)
*   实现简单的神经网络[第一部分](http://www.tech-quantum.com/implement-a-simple-neural-network-in-csharp-net-part-1/)和[第二部分](http://www.tech-quantum.com/implement-a-simple-neural-network-in-c-net-part-2/)

但是，当我们开始考虑一个非常大的 10 层 100 个神经元的网络时，几乎不可能进行手工计算或执行循环，这将是非常低效的。写出所有的计算将是一个巨大的…