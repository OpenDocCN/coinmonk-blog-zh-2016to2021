# 深潜— OpenZeppelin 的 ERC777 实施

> 原文：<https://medium.com/coinmonks/deep-dive-openzeppelins-erc777-implementation-9db978ce1bea?source=collection_archive---------0----------------------->

![](img/41529661517706edb694283d2eb5b57e.png)

Photo by [Jaap Straydog](https://unsplash.com/@rifje?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

OpenZeppelin 最近发布了他们对澎湃可替换令牌标准 ERC777 的实现。ERC777 的目的是改进 ERC20，同时保持向后兼容性。契约附带了两个钩子，`tokensToSend`和`tokensReceived`，地址可以实现这两个钩子来控制和恢复令牌操作。账户现在可以在单笔交易中接收资金和通知，取代了 ERC20 中的两步流程(`approve` / `transferFrom`)。让我们直接开始吧。

# 界面

777 和 ERC20 的一个显著区别是增加了运算符。令牌持有者可以授权和撤销受信任的实体代表他们行事。合同部署者可以定义默认操作员，这些操作员可以为所有地址移动令牌。请注意，`send`被用来代替`transfer`和`transferFrom`，反映了乙醚的转移。

# 合同

我们从契约定义和变量声明开始。ERC777 继承了 EIP 中定义的接口以及 ERC20 接口。需要一个自省注册表(ERC1820 ),契约和常规地址在其中发布它们实现的功能。当我们调用发送/接收钩子时，这两个硬编码的散列将会派上用场。

构造函数接受三个参数:令牌的`name`、令牌的`symbol`( DAI、BAT…等)和一个保存地址列表的数组`defaultOperators`。私有变量被赋值。然后，合同声明其 ERC777/ERC20 与注册中心接口。

`view`函数看起来和预期的一样。ERC20 合规性要求实施`decimals`。

# 发送

我们已经到达了`send`，这是在账户之间移动代币的典型方法。嵌套在里面的`_send`调用也将被`operatorSend`调用(我们稍后会讲到)。

`_send`要求 from 和 to 不能是零地址。注意`_move`是移动指针的那个(并发射两个`events`，每个令牌标准一个)。`_callTokensToSend` & `_callTokensReceived`是负责调用前面提到的钩子函数的二人组。

# 发送挂钩

`_callTokensToSend`首先用自检注册表检查我们事务中的`from`地址是否实现了 send-hook。这使我们能够调用`IERC777Sender(implementer).tokensToSend`。一旦触发，`from`地址应该会收到一个提示(这将取决于接口的 wallet 实现)，允许发送者恢复交易。

# 接收钩

`_callTokensReceived`应该觉得熟悉。如果`requireReceptionAck`为`true` & & `to`为合同，则末尾的`else if`会恢复。只有当一个 ERC20 函数(`transfer`、`transferFrom`)调用`_callTokensReceived`时`requireReceptionAck`才是`false`。回想一下，ERC777 继承了 ERC20。在调用`tokensReceived`时，接收者将得到通知，有人正在向他们发送一些令牌，并允许接收者恢复。

# 铸造

一个 777 派生的契约意味着调用`_mint`，因为作者选择在令牌创建方法中保持不可知论。状态变量被更新以反映铸造。只要接收钩准备就绪，新铸造硬币的接收者就会得到通知。最后，该方法发出`Minted` (ERC777)和`Transfer` (ERC20)事件。

# 烧伤

函数`burn`和`operatorBurn`都调用底层的`_burn`方法。发送挂钩允许刻录机恢复。`_totalSupply`和`_balances[from]`适当更新。

# 操作员发送/刻录

正如所料，这些函数代表令牌持有者调用`_send`和`_burn`方法。

# 操作员效用

`authorizeOperator` & `revokeOperator`都要求`msg.sender`不是所提供的参数。这两个函数都检查`operator`是否是默认操作符列表的一部分，以便修改相应的数组。

`isOperatorFor`检查给定用户是否授权给定操作员。

# 向后兼容性

ERC20 的所有功能均已实现。

# 资源

*   [EIP-777 标准](https://eips.ethereum.org/EIPS/eip-777)
*   [OpenZeppelin Github](https://github.com/OpenZeppelin/openzeppelin-solidity) / [实现](https://github.com/OpenZeppelin/openzeppelin-solidity/tree/master/contracts/token/ERC777)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)