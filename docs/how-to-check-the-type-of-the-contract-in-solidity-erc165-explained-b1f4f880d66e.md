# 如何在 Solidity 中检查合同类型— ERC165 解释

> 原文：<https://medium.com/coinmonks/how-to-check-the-type-of-the-contract-in-solidity-erc165-explained-b1f4f880d66e?source=collection_archive---------4----------------------->

![](img/e4a9e377a3b388d8c697cb1837e8f31d.png)

坚固没有`typeof`或`isinstance`的概念。给定一个合同地址，你不能发现这是否是一个特定的合同。但是如果合同已经实现了`ERC165`标准，就有办法做到这一点。

`ERC165`标准只有一种方法

```
**function** supportsInterface(interfaceId) **public** **view** **returns** (**bool**);
```

该方法将接口 id 作为参数，并检查协定是否支持该接口。这是什么意思？Solidity 有一个名为`type`的内置方法，它返回接口的唯一 id。您将在此处提供的接口属于您想要签入另一个协定的协定类型。

```
**type**(MyInterface).interfaceId
```

OpenZepplin 已经创建了一个名为`ERC165Storage`的实用契约，它实现了`ERC165`标准，并且有一个方法可以用来定义契约支持哪些接口。

```
**function** _registerInterface(**bytes4** interfaceId) **internal**;
```

可以向该方法提供一个接口 id，协定将保存该接口 id。如果向同一个接口查询支持，它将返回 true。

OpenZepplin 已经创建了`ERC165Checker`来调用某个地址上的`supportsInterface`。

```
**function** supportsInterface(**address** account, **bytes4** interfaceId) **internal** **view** **returns** (**bool**);
```

这个方法在地址上调用`supportsInterface`来检查地址是否支持给定的`interfaceId`。

这个怎么用？当您想要检查给定地址是否属于特定合同时，这很有用。但是你应该事先知道给定的地址已经实现了`ERC165`。这里有一个你将如何使用它的例子。

下面的契约继承了`ERC165`并在构造函数中调用了`_registerInterface`方法。

```
**contract** Sum **is** ERC165Storage, ISum { **constructor** {
		_registerInterface(**type**(ISum).interfaceId);
	} **function** sum(**uint256** a, **uint256** b) **public** **override** **returns** (**uint256**) {
		**return** a **+** b;
	}}
```

下面的合同想要检查给定的地址是否属于特定的合同类型。

```
**contract** Checker **is** ERC165Checker {

    **function** sumOrZero(**address** _add, **uint256** a, **uint256** b) **public** **view** **returns** (**uint256**) {
        **uint256** answer;
	**if** (supportsInterface(_add, **type**(ISum).interfaceId){
	    answer **=** ISum(_add).sum(a, b);
	}
	**return** answer;
    }
}
```

当您想要限制可以提供给函数的契约类型时，这很有用。您希望允许的每个合同都需要实现`ERC165`。

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)了解加密交易和投资