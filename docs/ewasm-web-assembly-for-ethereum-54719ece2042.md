# ewasm:以太坊的 Web 程序集

> 原文：<https://medium.com/coinmonks/ewasm-web-assembly-for-ethereum-54719ece2042?source=collection_archive---------1----------------------->

网络组装*又名*。wasm 在设计任何新的系统级的东西时总是优先考虑。这是为基于堆栈的虚拟机设计的二进制指令格式。或者，

你可以把它看作一个"*桥梁，在这里你可以用 C/C++/Rust、Go 或者除了 JavaScript 之外的任何其他语言编写代码，然后在你的浏览器上运行它。但是它不是 JavaScript"* (将在下一篇文章中讨论)的替代品

![](img/6cca83e88ac934457854db369b4e8fc2.png)

Imge credit: [https://unsplash.com/](https://unsplash.com/)

一般来说，整个过程分两步进行。首先，C/C++/Rust/Go 的所有代码都变成了名为`.wat`(**W**EB**A**assembly**T**ext)的文本格式

在下一步中，它被转换为`.wasm`二进制格式，这是一种较低级的格式，旨在供 wasm 虚拟机直接使用。

它看起来怎么样:

我正在写一个简单的 C 代码，将用于转换成。wasm 文件。

```
int add42(int num) {
  return num + 42;
}
```

在,`.wat`文件的下一步看起来是这样的:

```
(module
 (table 0 anyfunc)
 (memory $0 1)
 (export "memory" (memory $0))
 (export "add42" (func $add42))
 (func $add42 (; 0 ;) (param $0 i32) (result i32)
  (i32.add
   (get_local $0)
   (i32.const 42)
  )
 )
)
```

这将给出“二进制”格式的代码，它以十六进制表示法显示，但可以很容易地转换成二进制表示法。

```
00 61 73 6D 0D 00 00 00 01 86 80 80 80 00 01 60
01 7F 01 7F 03 82 80 80 80 00 01 00 04 84 80 80
80 00 01 70 00 00 05 83 80 80 80 00 01 00 01 06
81 80 80 80 00 00 07 96 80 80 80 00 02 06 6D 65
6D 6F 72 79 02 00 09 5F 5A 35 61 64 64 34 32 69
00 00 0A 8D 80 80 80 00 01 87 80 80 80 00 00 20
00 41 2A 6A 0B
```

之后，这段代码将在任何类型的硬件上运行(x86、ADM 或 any)

使用 Wasm 的几个原因:

*   性能:JS 和 WebAssembly 之间更快的加载时间和函数调用。
*   堆垛机；这可以解耦计量，使其更具性能，并可以转换到目标架构上的任何机器
*   共享工具/更广泛的工具兼容性。
*   直接使用 DOM(后 MVP)。

# 为什么选择 ewasm？

截至目前，我们的智能合约运行在虚拟机上。通过以确定的方式使它成为 Wasm 的子集，它可以具有如下优点。

1.  智能合约开发可以在 C/C++/Rust/Go 中完成。这将给生态系统带来巨大的好处。
2.  智能合同执行将更加快速。
3.  访问大量 Wasm 社区和工具。
4.  它将具有 *ewasm 契约*语义和*以太坊接口的规范。*
5.  灵活的计量注射器。计量是计算一些特定的操作集并执行它们所需的气体量。

EF 目前的开发可以在 [Github](https://github.com/ewasm) 找到。

参考资料:[https://web assembly . github . io/spec/core/_ download/web assembly . pdf](https://webassembly.github.io/spec/core/_download/WebAssembly.pdf)

干杯，

[Ankit Raj](https://twitter.com/a4nkit)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)