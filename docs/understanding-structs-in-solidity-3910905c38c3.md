# 理解固体中的结构

> 原文：<https://medium.com/coinmonks/understanding-structs-in-solidity-3910905c38c3?source=collection_archive---------2----------------------->

![](img/08f993900cba2973f871271d1ba62381.png)

使用 Struct 可以在 Solidity 中定义自定义类型。它可以由几个变量组成。

您可以查看 https://docs.soliditylang.org/en/v0.6.8/types.html? solidity @[官方文件中的参考文件突出显示=结构#结构](https://docs.soliditylang.org/en/v0.6.8/types.html?highlight=struct#structs)

示例:

```
struct User {
    uint level;
    uint status;
}mapping(address => User) public userList;User userDetail = User(1, 1); function updateUserLevel(uint userLevel) public { 
    userDetail.level = userLevel;
}function getUserDetail(address userAddress) public returns (User memory) {
    return userList[userAddress];
}
```

代码的完整工作示例可以在@ [这里](https://gist.github.com/sagarduwal/71ab106116372057ffcc87d6efaf8f94)找到。

在上面的例子中，我们已经创建了一个结构`User`，并使用`User`作为类型创建了变量`userDetail`，并将其用作变量的类型。

我们已经使用关键字`struct`在 Solidity 中定义了 struct 数据类型，以及在花括号中定义的 struct 名称和变量名。

```
struct StructName {
    string name;
    uint value;
}
```

在上面的代码中，我们在第` User userDetail = User(1，1)行中定义了 struct'这是定义 struct 的最短方法。

我们也可以用其他方式定义 struct。

```
function createUserRecord (address userAddress_, uint level_, uint status_) public {
    User storage newRecord = userList[userAddress];
    newRecord.level = level_;
    newRecord.status = status_;
}function createUserRecordTwo (address userAddress_, uint level_, uint status_) public {
    userList[userAddress_] = User({
        level : level_,   status : status_
    });
}
```

所以，以上是另外两种使用 struct 和 create object 的方法，并且是在`userList`中设置的映射(这一点我们将在另一个教程中看到)。

**注意**:带结构的完整代码示例可以在这里找到[**structexamplefull . sol**](https://gist.github.com/sagarduwal/65795273b0b2cc514169d2f2b39605cd#file-structexamplefull-sol)

使用 Struct 有一些限制，

*   函数不能返回结构数据，除非其内部函数调用
*   `struct`不能递归 ie。结构内部的结构类型相同

```
struct User {
    uint level;
    uint status;
    User refererUser; // this will return error since recursive
}
```