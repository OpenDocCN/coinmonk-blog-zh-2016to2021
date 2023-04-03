# 坚固性基础

> 原文：<https://medium.com/coinmonks/solidity-fundamentals-88ceb4873064?source=collection_archive---------1----------------------->

## 以太坊智能合约开发

## 合同的格式

![](img/1fd15522f6c3418e4f9305f295babcbb.png)

**先简单说一下这个系列是关于什么的。这里有一些我们将要讨论的话题，我希望你会喜欢。**

*   *坚实的基础*
*   *基础设施(Metamask，Infura，DappNode)*
*   *测试以太坊智能合约(华夫饼、松露套件、Dapp 工具)*
*   *智能合约安全最佳实践*

![](img/5bd5a4d68e25ba312956b9f612db200b.png)

# 坚固性基础

在这一部分，我们将简要检查一个实体源文件的布局。我鼓励你现在不要运行给定的代码配方，而是观察和理解每一条给定的信息。稍后，我们将编写足够的代码来熟悉所有的概念。

[GIPHY](https://giphy.com/)

每个源文件都应该以一个注释开始，说明它的许可证，例如:

`pragma` 关键字指定什么版本的编译器对编译这个合同文件有效。更多细节将在后面讨论。

Solidity 支持导入语句来帮助模块化你的代码，类似于那些在 **JavaScript** 中可用的语句(从 ES6 开始)。然而，Solidity 不支持默认导出的概念。参见下面的例子；

对于记录代码，使用注释会非常有帮助。

三斜线(///)或双星形块(/** */)称为 **natspec** 注释，直接用在函数声明或语句的上方，给出关于该函数或语句的信息。

根据 solidity 官方文件，组织合同布局的最佳实践如下。

按以下顺序布置*合同要素*:

1.  杂注语句
2.  导入报表
3.  接口
4.  图书馆
5.  契约

在每个*合同*、*库*或*接口*中，使用以下顺序:

1.  状态变量
2.  事件
3.  功能修饰符
4.  结构、数组或枚举
5.  构造器
6.  回退—接收功能
7.  外部可见功能
8.  公共可见功能
9.  内部可见函数
10.  私有可见函数

合同布局的配方示例。请仔细阅读评论和检查下面的例子，集中在布局而不是给定代码的逻辑。

下一部分我们将研究**值类型**，比如布尔值、整数、地址以及动态大小和固定大小的类型——字节和字符串。感谢阅读。

**参考文献**

 [## 坚固性-坚固性 0.7.5 文件

### Solidity 是一种面向对象的高级语言，用于实现智能合约。智能合同是程序…

docs.soliditylang.org](https://docs.soliditylang.org/en/v0.7.5/index.html) 

# 所有部件正常

*   [合同的布局](https://ferdikurt.medium.com/solidity-fundamentals-88ceb4873064)
*   [值类型:第一部分](https://ferdikurt.medium.com/solidity-fundamentals-ca824a3ac023)
*   [值类型:第二部分](https://ferdikurt.medium.com/solidity-fundamentals-c94460e3be3d)
*   [运算符](/coinmonks/solidity-fundamentals-1fb0e6b3b607)
*   [参考类型](/coinmonks/solidity-fundamentals-e4e4660e16c8)
*   [数据位置和赋值行为](/coinmonks/solidity-fundamentals-a71bf54c0b98)
*   [控制结构](/coinmonks/solidity-fundamentals-1dc2af4b453b)
*   [错误处理:断言、要求、恢复和异常](/coinmonks/solidity-fundamentals-a95bb6c8ba2a)
*   [功能:第一部分](/coinmonks/solidity-fundamentals-d2216be1c2c3)
*   [功能:第二部分](https://ferdikurt.medium.com/solidity-fundamentals-functions-a7e3d38c1fe5)

随便问任何问题。

好好保重，好好工作，保持联系！

[费尔迪科特](https://www.linkedin.com/in/ferdi-kurt-8b910b164/)