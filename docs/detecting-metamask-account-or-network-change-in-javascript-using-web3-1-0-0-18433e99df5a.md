# 使用 Web3 1.0.0 检测 Javascript 中的元掩码帐户或网络更改

> 原文：<https://medium.com/coinmonks/detecting-metamask-account-or-network-change-in-javascript-using-web3-1-0-0-18433e99df5a?source=collection_archive---------0----------------------->

![](img/b47e6ffe50892a3abc921943f64aff1b.png)

开发 dApps 的一个常见问题是检测用户打算使用元掩码中的哪个帐户。在撰写本文时，即使是 Metamask 也建议每 100 毫秒轮询一次。 [(Eww)](https://github.com/MetaMask/faq/blob/master/DEVELOPERS.md#ear-listening-for-selected-account-changes)

```
//Not a fun pattern to have to usevar account = web3.eth.accounts[0];
var accountInterval = setInterval(function() {
  if (web3.eth.accounts[0] !== account) {
    account = web3.eth.accounts[0];
    updateInterface();
  }
}, 100);
```

相反，由 web3 1.0.0 中的元掩码提供程序公开的 publicConfigStore 发出“更新”事件。连接到它就像:

```
web3.currentProvider.publicConfigStore.on('update', callback);
```

将使用包含键的对象调用您的回调:{selectedAddress，networkVersion}。您可以使用它来轻松地监听地址或网络变化，并相应地更新您的 UI。

> [发现并回顾最佳区块链软件](https://coincodecap.com)

这里需要注意的是，由于上面 Github 上的链接自述文件建议了轮询方法，publicConfigStore 在本文撰写时是一个不稳定的特性。就像现在 crypto 中的其他东西一样，你可以自行决定使用！

和我一起做梦: [www.dream.associates](http://www.dream.associates)