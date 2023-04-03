# 学习 Apache Flink 的旅程(3)——未经确认的比特币交易流

> 原文：<https://medium.com/coinmonks/the-journey-of-learning-apache-flink-3-streaming-unconfirmed-bitcoin-transactions-f4d636e77de9?source=collection_archive---------8----------------------->

到目前为止，我们已经介绍了 1) [初始 EMR 和项目设置](/@chengzhizhao/the-journey-of-learning-apache-flink-1-initial-emr-and-project-setup-b43a5ef69c77) 2) [简单的维基百科流](http://Simple Wikipedia Streaming)。它们都是现成的，如果我们想从头开始构建流媒体应用，请继续阅读。

我们将构建一个包含一些热门话题的流应用程序，帮助您更好地理解 Flink。但是我们还没有涉及太多数据来自哪里。Flink 提供了预定义的源，使事情变得更容易，你可以在这里找到详细的列表…