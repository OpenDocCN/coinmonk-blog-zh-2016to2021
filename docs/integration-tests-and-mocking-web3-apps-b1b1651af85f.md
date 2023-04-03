# 集成测试和模仿 web3 应用

> 原文：<https://medium.com/coinmonks/integration-tests-and-mocking-web3-apps-b1b1651af85f?source=collection_archive---------5----------------------->

![](img/c24a03261f16a79086dcbe370bdefbbb.png)

[source](https://twitter.com/web3foundation)

分散式应用程序带来了一系列新的挑战。其中之一是测试。事务生命周期比老派的 POST 请求/响应流程更复杂，并且错误通常没有什么帮助。尽管开发人员的体验越来越好，但这也让我们看到了测试的重要性。

```
Error: VM Exception while processing transaction: revert
```

是新的

```
Uncaught TypeError: undefined is not a function
```

从开发人员的角度来看，从 Web 2.0 后端切换到 Ethereum dApps 时最显著的变化是，您不能期望响应中立即出现“请求”返回值。在您发送事务后，事务哈希就可用了，但这并不意味着事务会成功，甚至不意味着矿工会将它包括到区块链中。这就是 React/Redux 应用程序中处理事务的方式。

```
**this**.props.addTransaction({ id });
AwesomeContract.methods.awesomeMethod(web3.utils.asciiToHex(values.awesomeString))
  .send({ from: account })
  .on("transactionHash", txhash **=>** **this**.props.setTransactionHash({ id, txhash }))
  .on("receipt", receipt **=>** **this**.props.setTransactionReceipt({ id, receipt }))
  .on("error", error **=>** **this**.props.setTransactionError({ id, error }))
  .on("confirmation", confirmation **=>** **this**.props.setTransactionConfirmation({ id, confirmation }));
```

测试每个函数来处理事务的特定状态是非常容易的。如果您在 reducers 中实现处理事务的业务逻辑，那就更是如此。

测试整个流程是一个相当大的挑战，需要大量的模仿来使它成为一个快速完成的单元测试。这就是为什么我更喜欢用集成测试来测试它。

最近，我喜欢使用 Cypress 进行测试，这是一种非常好的、没有麻烦的开发体验。Cypress 正在运行 chrome 的一个实例，当然，它缺少 window 对象上的 web3 实例。无论您使用哪种集成测试工具，无法访问 MetaMask 或 Mist 都将是一个常见的问题。我的解决方案是注入 web3 实例，它不需要任何用户操作来签署事务。通过附加到`window:before:load`事件，我们可以在应用程序代码运行之前修改窗口对象。

```
**import** Web3 from "web3";
**import** PrivateKeyProvider from "truffle-privatekey-provider";

cy.on("window:before:load", (win) **=>** {
  **const** provider **=** **new** PrivateKeyProvider(Cypress.env("ETH_PRIV_KEY"), Cypress.env("ETH_PROVIDER"));
  win.web3 **=** **new** Web3(provider); *// eslint-disable-line no-param-reassign*
});
```

有几种方法可以在 Cypress 中使用[环境变量。不要让`truffle-privatekey-provider`的名字欺骗了你。这不是一个松露依赖包。](https://docs.cypress.io/guides/guides/environment-variables.html)

在测试中使用 PrivateKeyProvider 的可能性并没有到此为止。您还可以通过直接从测试场景代码中进行事务来测试您的应用程序 UI 如何对“另一个用户”触发的事件做出反应。我希望这给你一些如何测试你的 dApp 的见解。

*原载于 2018 年 5 月 26 日*[*michalzalecki.com*](https://michalzalecki.com/integration-tests-and-mocking-web3-apps/)*。*