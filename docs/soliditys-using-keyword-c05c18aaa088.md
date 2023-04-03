# Solidity 的“使用”关键字

> 原文：<https://medium.com/coinmonks/soliditys-using-keyword-c05c18aaa088?source=collection_archive---------3----------------------->

有时候，人们会想知道自己喜欢的编程语言中某些特性背后的含义。Lukas Cremer ，Gerard 和我是来自德国柏林的区块链开发者，我们挑战自己是否能解释 solidity 的关键字`using`的用法。

`using`用于在 solidity 合同中包含一个库。检查以下示例:

```
pragma solidity ^0.4.15;library SomeLibrary  { function add(uint self, uint b) returns (uint) {
   return self+b;
 }
}contract SomeContract {

    using SomeLibrary for uint;

    function add3(uint number) returns (uint) {
        return number.add(3);    
    }
}
```

代码`using SomeLibrary for uint;`允许我们在这个例子中在`function add3`中使用`return number.add(3);`。它基本上是以下内容的语法糖:

```
pragma solidity ^0.4.15;library SomeLibrary  {function add(uint self, uint b) returns (uint) {
   return self+b;
 }
}contract SomeContract {

    function add3(uint number) returns (uint) {
        return SomeLibrary.add(number, 3);    
    }
}
```

正如我们所见，通过添加关键字`using`，我们可以给`SomeContract`中的任何`uint`提供库函数，并将`uint`作为该函数的第一个参数传递。

现在让我们看另一个例子:

```
pragma solidity ^0.4.15;library SomeOtherLibrary  { function add(uint self, uint b) returns (uint) {
     return self+b;
   } function checkCondition(bool value) returns (bool)  {
      return value;
   }}contract SomeContract {
    using SomeOtherLibrary for *;function add3(uint number) returns (uint) {
        return number.add(3);    
    }

    function checkForTruthy(bool checker) returns (bool) {
        return checker.checkCondition();    
    }
}
```

这里的`*`允许在契约中访问来自`SomeOtherLibrary`的任何类型。

我们意识到的另一件事是，这也适用于`array`和`struct`类型，但是需要声明`storage`关键字。但这是另一篇博文的主题。

感谢 Lukas Cremer 和 Gerard Baecker 对这篇博文的帮助。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)[![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)