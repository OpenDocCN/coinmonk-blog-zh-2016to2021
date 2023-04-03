# 高级 EOS 系列—第 1 部分—加密哈希

> 原文：<https://medium.com/coinmonks/advanced-eos-series-part-1-cryptographic-hashes-a251a8d371b8?source=collection_archive---------3----------------------->

![](img/a1f6d3c7053ad71dfcfa25748d5a7afd.png)

欢迎来到高级 EOS 开发系列，在这里我将涉及到教程和课程很少涉及的技术和功能。本系列的目的是将您作为应用程序开发人员在 EOS 网络上完成技能所需的缺失部分汇集在一起。每篇文章都是按照难度排序的，所以如果你想要一个大概的了解，我建议你从这里开始，一步步往上。这些例子的完整代码可以在 GitHub 上找到[。](https://github.com/MitchPierias/Advanced-EOS-Examples)

[](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/01_Hashes) [## MitchPierias/高级-EOS-示例

### 一系列 EOS 合同开发实例。通过创建…为 MitchPierias/Advanced-EOS-Examples 开发做出贡献

github.com](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/01_Hashes) 

> 由于这些是高级或扩展的主题，我很危险地假设你已经知道了基础知识，并且正在寻求进一步的知识。因此，这些文章中共享的代码将针对所讨论的主题进行精简。

# 加密哈希函数

让我们从哈希开始，这个例子的所有资源都可以在 GitHub 的[这里找到。在密码术中，散列算法接受输入并生成固定大小的混杂信息，称为输出摘要。摘要是确定的和不可逆的，这意味着相同的输入将总是生成相同的输出，并且输出不能被反转以获得原始输入。一个好的哈希算法通过在最短的计算时间内使用“高难度”来最小化输出冲突。两个不同的输入产生相同输出的可能性。](https://github.com/MitchPierias/Advanced-EOS-Examples/tree/master/01_Hashes)

## 哈希的应用

通常我们会使用哈希函数来认证和索引数据，我喜欢把它看作是一种压缩唯一性的方法。例如，比较两个任意大小的文件或用户电子邮件会非常耗时，并且会暴露易受攻击的用户数据。我们可以通过散列文件内容或用户电子邮件来生成指纹，并使用散列结果更有效地索引和比较数据。

这大概是我们目前需要知道的，但是如果你想满足你的好奇心，那么我会鼓励你主动谷歌一下或者看看 Blockgeeks 关于[加密散列函数](https://blockgeeks.com/guides/cryptographic-hash-functions/)的文章。

# 编写我们的哈希函数

现在，我们对哈希有了非常基本的了解，我们可以看看如何在我们的 EOS 智能合约中生成我们自己的哈希。我们将创建一个动作，它接受一个输入`string`并输出一个`checksum256`散列摘要，然后为了这个例子的目的，打印结果。

让我们从包含来自 EOS 框架的`crypto.h`库和 EOS `print.h` 包装器开始。

```
include <eosiolib/crypto.h>
```

> 注意*。我们的`crypto.h`文件名上的 h* 扩展名？这意味着我们使用的是一个 *C* 库，而不是 *C++* ，所以我们的方法需要 *C* 类型的输入。

现在我们可以像这样定义我们的动作并为我们的`checksum256`输出分配一个引用。

```
checksum256 sum{};
```

EOS 框架为各种散列算法提供了一套方法。在这个例子中，我们将使用`sha256`,因为它快速、安全，是大多数情况下使用的典型。

```
sha256(const_cast<char*>(str.c_str()), str.size(), &sum);
```

我们的`sha256`方法需要 *C* 类型的输入，所以我们首先需要将我们的`string`转换成一个`char`集合，然后传递`string`的大小和一个`checksum256`引用，这将使用输出摘要进行更新。现在剩下的就是打印散列的输出结果。

```
printhex(&sum, sizeof(sum));
```

我们也可以在这里返回`checksum256`,并将其用作私有的内部操作。

# 更进一步— **字符串校验和**

> 这个实现归功于 [Miguel Mota](https://github.com/miguelmota) 开发了一个将 checksum256 转换为十六进制字符串的解决方案。

那么，如果我们想把摘要转换回字符串，会发生什么呢？为此，我们需要一种新的方法。首先，让我们为输入参数定义一个模板，并定义方法本身。这个模板是在我们的方法定义之外定义的，通常在我们文件的顶部。模板只是允许我们在调用函数时定义变量类型，然后编译器会负责替换并使其正常工作。

```
template <typename CharT>string to_hex(const CharT* data, uint32_t length) {
  // Method Body
}
```

现在我们可以开始处理我们方法的主体了。首先，我们将为我们的结果创建一个`string`，一个`char`集合，用于稍后绘制字符，并将我们的数据强制到一个`uint8_t`。

```
string result;
const char* hex_chars = "0123456789abcdef";
uint8_t* c = (uint8_t*)data;
```

最后，我们将迭代输入数据中的每个`char`，匹配相应的十六进制字符，并将其添加到结果`string`中。

```
for (uint32_t i = 0; i < length; ++i) {
  (result += hex_chars[(c[i] >> 4)]) += hex_chars[(c[i] & 0x0f)];
}return result;
```

就是这样！我们现在有了一个从`char`集合创建`checksum256`的方法，以及一个将`checksum256`转换成`string`的方法。在下一篇文章中，我们将关注单体，以及如何利用它们来存储应用程序状态和配置。

## 下一步是什么？

如果您想在我分享本系列中的更多示例时得到通知，请务必点击*跟随*按钮。第 2 部分将介绍我们如何利用 EOS 中的单例来存储契约状态和配置。

[![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)

[**Click to read today’s top story**](http://bit.ly/2G71Sp7)