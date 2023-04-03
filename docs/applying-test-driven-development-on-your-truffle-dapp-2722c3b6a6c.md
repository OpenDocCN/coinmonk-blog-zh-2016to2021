# 在你的 Truffle Dapp 上应用测试驱动开发

> 原文：<https://medium.com/coinmonks/applying-test-driven-development-on-your-truffle-dapp-2722c3b6a6c?source=collection_archive---------2----------------------->

![](img/0754f5260c52a410ba4f203188dd70ca.png)

测试驱动开发(TDD)是一个软件开发过程，它依赖于一个非常短的开发周期的重复:需求被转化为非常具体的测试用例，然后软件被改进以通过测试。(您在实现特性之前编写测试用例)

安装松露

```
npm i truffle
```

下载松露盒

```
truffle unbox yehia67/HelloWorldBox
```

然后转到 test/Simple.test.js

你应该会发现这个内容

```
const simple = artifacts.require(‘./Simple.sol’)contract(‘Simple’, (accounts) => {before(async() => {this.Simple = await simple.deployed()})it(‘deploys successfully’, async() => {const address = await this.Simple.addressassert.notEqual(address, 0x0)assert.notEqual(address, ‘’)assert.notEqual(address, null)assert.notEqual(address, undefined)})it(‘Set Function work well’, async() => {const simple = await this.Simple.set(“Simple Contract”)const Hello = await this.Simple.hello()assert.equal(Hello, “Simple Contract”)})it(‘Get Function work well’, async() => {const simple = await this.Simple.set(“Simple Contract”)const Hello = await this.Simple.get()assert.equal(Hello, “Simple Contract”)})})
```

**代码解释**

首先，初始化契约并部署它。怎么会？

导入合同:

```
const simple = artifacts.require(‘./Simple.sol’)
```

使用`contract`函数部署合同，第一个参数是合同名称，第二个参数是回调函数以导入地址帐户。

```
contract(‘Simple’, (accounts) => {before(async() => {this.Simple = await simple.deployed()})
 //place test cases here
```

现在我们已经准备好创建我们的测试用例，因为我们有了`this.Simple`对象，我们可以使用它调用任何函数或变量。例如，如果我们有一个名为`myfunction`的函数，我们可以这样叫它 `await this.Simple.myfunction ()`

为了制作测试用例，我们将使用`await`关键字。为了使用`await`，它应该在`async`函数内部。所以测试用例会是这样的

```
// 3 test cases// Check if contact deployed successfully 
it(‘deploys successfully’, async() => {const address = await this.Simple.addressassert.notEqual(address, 0x0)assert.notEqual(address, ‘’)assert.notEqual(address, null)assert.notEqual(address, undefined)})//Check if Set Function working correctly
it(‘Set Function work well’, async() => {const simple = await this.Simple.set(“Simple Contract”)const Hello = await this.Simple.hello() //check hello variable assert.equal(Hello, “Simple Contract”)})//Check if Get Function working correctlyit(‘Get Function work well’, async() => {const simple = await this.Simple.set(“Simple Contract”)const Hello = await this.Simple.get()assert.equal(Hello, “Simple Contract”)})
```

现在，回到我们的目标，我们将如何应用 TDD？在这个简单的例子中，您可以轻松地执行以下操作

1-决定我要创建多少个合同(在我们的例子中只有一个)

2-您想要创建多少个函数(只有 2 个)

3-为每个合同创建文件。

4-按照上面的例子编写测试用例。

5-运行该命令 `truffle test ./path/to/test/fileName.test.js`

你可以写你的测试用例，然后开发你的联系人，然后测试每一个。希望这有用。谢谢，如果你有任何问题，请随时提问。

[![](img/a06b758bdcc47dca7c2504f298674d87.png)](https://coincodecap.com)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)