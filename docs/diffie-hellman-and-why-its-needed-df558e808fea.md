# Diffie Hellman 和为什么需要它

> 原文：<https://medium.com/coinmonks/diffie-hellman-and-why-its-needed-df558e808fea?source=collection_archive---------3----------------------->

回顾 Diffie Hellman 和 Signal 对 Diffie Hellman(棘轮协议)的实现。

Diffie Hellman 是一种端到端的加密密钥交换方法，它允许双方通过向每一方提供足够的信息来获得相同的秘密，而无需共享该秘密，从而实现安全通信。由 Ralph Merkle、Whitfield Diffie 和 Martin Hellman 于 1976 年开发，由英国信号情报局发现。

使用 Diffie Hellman 增强安全性的一些协议有:

*   SSH(安全外壳)