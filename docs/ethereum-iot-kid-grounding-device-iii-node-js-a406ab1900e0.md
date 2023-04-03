# 以太坊 IOT 小子接地装置三:Node.js

> 原文：<https://medium.com/coinmonks/ethereum-iot-kid-grounding-device-iii-node-js-a406ab1900e0?source=collection_archive---------4----------------------->

![](img/52c00261187f4b4006fac2899b9eb3e2.png)

Photo by [Philippe Verheyden](https://unsplash.com/photos/SNvTszJG-WU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/asian-kid?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

这是我的以太坊 IOT 儿童地面设备项目的第三部分。在这一部分，我们将讨论用 [Node.js](https://nodejs.org/en/) 编写并作为服务器应用程序运行的 web 服务后端的[代码](https://github.com/jacksonng77/Ethereum-Grounder/blob/master/Node/index.js)。你可以在这里阅读第一部[，在这里](/coinmonks/internet-of-trusted-things-ethereum-kid-grounding-device-part-1-how-it-works-3cb1e8dd7db0)阅读第二部[。](/coinmonks/ethereum-iot-kid-grounding-device-ii-smart-contract-67e1f544aade)

在我的项目中，web 服务是以太坊交易真正签名和执行的地方。Node.js 后端接受来自 PIR 传感器的消息…