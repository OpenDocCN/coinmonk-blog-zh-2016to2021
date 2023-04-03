# 用 C#实现一个简单的神经网络。网络—第 1 部分

> 原文：<https://medium.com/coinmonks/implement-a-simple-neural-network-in-c-net-part-1-d44b49ae6e99?source=collection_archive---------0----------------------->

上一篇文章“ [**神经元的功能**](http://www.tech-quantum.com/function-of-the-neuron/) ”，我们看到了人工神经元如何在与门数据上进行手动训练。我们看到，在第 6 次迭代后，一个简单的网络学会了，现在能够预测。让我们以代码的形式将学习内容放在一起，以编程的方式完成。我将这篇文章分为两部分:

1.  创建神经网络结构
2.  使用反向传播算法用与门数据进行训练