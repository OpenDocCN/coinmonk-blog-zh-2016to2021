# 与 web3、Ganache CLI 和 Jest 的集成测试

> 原文：<https://medium.com/coinmonks/integration-tests-with-web3-ganache-cli-and-jest-f342b5f7af46?source=collection_archive---------3----------------------->

![](img/a38af86a70fe25a488911f03530d9f7e.png)

Photo by Louis Reed

写得好的一组测试在交付一个可靠的软件中起着至关重要的作用。一套好的测试套件可以确保应用程序按预期运行，并显著减少 bug 的数量。说起来容易做起来难。实现这个目标的步骤之一是坚持不同测试级别的界限。在本文中，我想把重点放在依赖于 web3 的分散式应用程序的集成测试上。对于集成测试，我可以牺牲执行速度来获得应用程序不同组件无缝协作的信心。

上一次，我在黑盒测试中将 web3 注入到窗口对象中时，提出了一个模仿 web3 的更加 [E2E 的解决方案。这种技术需要连接到以太坊节点，并在本地运行 Ganache。当您关心自动化测试的能力时，这是一个很好的解决方案。虽然这不是银弹。](https://michalzalecki.com/integration-tests-and-mocking-web3-apps/)

最近，我在浏览器上进行智能合约部署。为负责该功能的组件运行健壮的 E2E 测试用例太慢了。幸运的是，Ganache CLI 还提供了对自动挖掘后续块的提供程序的编程访问。我们可以使用它来快速启动 ganache 实例，并防止在测试重新运行之间共享区块链状态。

# Ganache CLI 提供程序

为了交换所选测试套件的提供者，我们使用 Jest 的模拟特性。让我们为 web3 提供者 getter 创建一个单独的模块。

```
*// web3Provider.ts*
**import** Web3 **from** "web3";

**export** **function** provider() {
  **return** Web3.givenProvider;
}
```

然后我们可以使用这个模块来实例化一个 web3。

```
*// web3.ts*
**import** Web3 **from** "web3";
**import** { provider } **from** "./web3Provider";

**export** **const** web3 **=** **new** Web3(provider());
```

我们用于测试的提供者的实现略有不同。

```
*// someModule.spec.ts*
jest.mock("./web3Provider", () **=>** {
  **function** provider() {
    **return** require("ganache-cli").provider();
  }
  **return** { provider };
});
```

在我们嘲笑提供者的测试案例中，所有事务都将在 ganache 上运行。这样，我们每次测试应用程序时都有一个新的区块链实例。

# 跳过慢速测试

尽管自动化 Ganache 启动并使其成为测试用例的一部分比作为单独的应用程序运行有所改进，但这仍然需要几秒钟的时间。这超出了我对运行我的单元测试感到舒服的程度。我想到了一个解决这个问题的方法。

我们用 slow description 包装运行缓慢的测试，而不是直接使用 Jest 的 description。

```
describe("BettorAgreement", () **=>** {
  slowDescribe("deploy", () **=>** {
    it("deploys a contract", **async** () **=>** {
      **const** [account] **=** **await** web3.eth.getAccounts();
      **const** contract **=** **await** deploy("1000000", { from: account, gas: 3000000 });
      expect(contract.options.address).toContain("0x");
    });
  });
});
```

如果`ALLOW_SLOW`环境变量等于假，我们完全跳过给定的测试集。

```
**function** allowSlow() {
  **return** `${process.env.ALLOW_SLOW}`.toLowerCase() **!==** "false";
}

**export** **function** slowDescribe(msg: **string**, handler: jest.EmptyFunction) {
  **if** (allowSlow()) {
    describe(msg, handler);
  } **else** {
    describe.skip(msg, handler);
  }
}
```

我有点反对仅仅为了在测试中进行一些黑客攻击而修改“生产”代码。一个月后，没有人会记得这种实施背后的意图是什么。尽管如此，您可以在提供者内部检查 allowSlow 的结果，并打赌 UglifyJS 可以从包中删除死代码。

```
*// web3Provider.ts*
**import** Web3 **from** "web3";

**export** **function** provider() {
  **if** (allowSlow()) **return** require("ganache-cli").provider();
  **return** Web3.givenProvider;
}
```

# 结论

JavaScript 的动态特性给了我们模仿和修补测试代码和实际实现的灵活性。记住，你的测试服也是你必须维护的代码，过度工程化是有代价的，所以尽量保持简单。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)

*原载于 2018 年 9 月 15 日*[*【michalzalecki.com*](https://michalzalecki.com/integration-tests-with-web3-ganache-cli-and-jest/)*。*