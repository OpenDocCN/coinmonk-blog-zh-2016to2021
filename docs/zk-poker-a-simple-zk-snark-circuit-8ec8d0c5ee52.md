# ZK 扑克——一个简单的 ZK-斯纳克电路

> 原文：<https://medium.com/coinmonks/zk-poker-a-simple-zk-snark-circuit-8ec8d0c5ee52?source=collection_archive---------0----------------------->

![](img/993e0acec22a33f7718070bb1b356b59.png)

知识证明是我们去中心化的一个重要工具。当我们的平台存储全球透明的数据时，我们如何维护隐私？那些确实在‘链外’工作的扩展解决方案呢？这些解决方案需要通过链内证据来证实。零知识证明在解决这类问题时发挥了作用。

当谈到为 ZK 电路编写代码时，我们有许多库可供选择。我在[身份 3](https://iden3.io/) 的团队中发现了 **circom** 和 **snarkjs** 。对于这个领域的新手来说，这是一个很好的选择:电路很容易编码，并且可以用简单的命令运行，不需要特殊的环境。

为学习 **circom** 和 **snarkjs** 提供的[教程](https://iden3.io/blog/circom-and-snarkjs-tutorial2.html)真的很有帮助。看到这个过程在你面前展开，我感到非常满意，这是一次很好的教育经历。本文旨在引导您超越该介绍性教程，并探索 **circom** 的其他一些方面。这是基于一个简单的，修改版本的扑克，其中零知识证明发挥了作用。

# ZK 扑克

ZK 扑克的规则比其他种类的游戏简单一些。

*   发给玩家 5 张牌，
*   不允许换卡。
*   排名时，只有对子才算数。顺子、同花、满座等等都被忽略。
*   玩家回合中的选项包括弃牌、观望或加注。
*   *如果手中没有对子，玩家不得叫牌。*

最后一条规则——*不要虚张声势——*是由 ZK 证明强制执行的。投标人可以避免自己的牌被公开，同时仍然向其他玩家证明他们遵守了*不虚张声势*规则。我们将忽略游戏的机制(评估赢家，评估出价等)，而只关注将产生 ZKP 的电路。

# 该电路

电路的大致轮廓是:

*   收集输入:手牌和玩家的叫牌选项
*   在手中寻找至少一对。
*   评估叫牌的种类(弃牌或玩牌)
*   设置一个约束来检查是否已经做出选择
*   设置一个约束条件，以检查在给定一对的情况下选择是否有效。

让我们一次浏览几行电路代码，看看在每一点上我们能学到什么。

## 包含

它从一些*开始，包括*规格:

```
include “../node_modules/circomlib/circuits/gates.circom”;
include “../node_modules/circomlib/circuits/comparators.circom”;
```

我应该指出，我的文件夹结构是:

<*项目根*>\扑克

所以我指定的包含路径适用于这个结构。您需要安装 **circomlib** ，这是入门 **circom** / **snarkjs** 教程的附加要求。在项目根文件夹中使用此命令:

```
npm install --save circomlib
```

## 电路模板

```
template Poker() { ... circuit body ...}component main = Poker();
```

其结构与介绍性教程中的基本相同。它是一个单独的类，入口点是声明的。又好又简单。

## 声明输入、输出和中间结果

```
signal private input cards[5]; // Each 2..14
 signal input isSee; // 1 or 0
 signal input isFold; // 1 or 0
 signal input raise; // int
 signal output out; // 1 or 0

 // Intermediate results
 signal isBid;
 signal isRaise;
 signal hasChosen;
```

牌的手被声明为一个数组:牌[5]。卡片只是用它们的面值来表示。西装被忽视了。Ace = 14 国王= 13；女王= 12；杰克= 11；以此类推，下降到 2。这只手是私人输入，表示它必须保密。

当然，投标选择是公开的，因为它将向其他参与者公开。*折叠*和*参见*选项表示为布尔值。请注意，这不是(也不能)显式声明的。 **Circom** 基于 javascript，因此变量类型是从数据中推断出来的。(我本应该向支持健壮数据类型的人发出触发警告。)第*次提升*输入为整数，为实际提升量。

唯一的输出是*出*，有效时取 1，无效时取 0。

部分中间值声明为*信号。*我们以后会看到他们的行动。

## 数对子

这个代码块决定了手是否包含一对。请注意，代码很像普通的 javascript。它不包含任何约束声明，也不包含任何信号操作。

这里有一个对电路工作方式的见解。现实生活中的电路可能涉及一些预处理或中间评估，但要小心:这样的代码不属于正式证明的一部分。在设置时，仅烘焙约束条件，并通过验证/检验周期进行确认。一个不诚实的证明者可能会颠覆中间结果来伪造证人。我真的应该更新代码，以便将约束内置到对评估中。

照现在的情况来看，预处理步骤产生了一个单一的变量*num compars*。这将在后续步骤中使用。

这里的逻辑是一个嵌套循环，依次考虑每张牌，并比较每张随后的牌，看是否有一对。一旦找到一对，我们就添加到*numparis*并退出。我们不感兴趣的是它是否真的是同类中的 3 对或 4 对，也不关心是否有第二对。出于我们将评估的约束的目的，所有这些可能性都被视为一对。还要注意的是 *break* 似乎并没有在这个经过裁剪的 javascript 版本中实现。这段代码只是通过强制迭代器变高来触发退出。

```
 // Count pairs
 var numPairs = 0;
 for (var i=0; i<4; i++) {
    for (var j=i+1; j<5; j++) {
       if (cards[i] == cards[j]) {
          numPairs++;
          // break doesn’t work. Just force j and i to exit
          j = 5;
          i = 5;
       }
    }
 }
```

## 限制

入门教程包括信号操作:`<--, -->, <==, ==>`和`===`。这段代码中的新内容是**组件**。组件是从 **circomlib** 库中导入的一个类的实例。注意这些调用的方式。组件的输入信号(名为 a、b、in 等。)使用信号运算符进行赋值。组件的输出信号(通常命名为 *out* )可以类似地使用。

这里，我们使用布尔运算符的组件。查看 [circomlib 代码](https://github.com/iden3/circomlib)看看有什么可用的。有一个全面的函数库，比如哈希运算、椭圆曲线运算等等。

因此，我们的约束被定义了。有一个约束来检查是否已经做出选择(盖牌、看牌或加注)。还有一种方法是检查是否有对子或者是否选择了弃牌。最后，将*输出*信号设置为 1。

```
 // isRaise = (raise != 0)
 isRaise <-- (raise > 0);
 isBid <-- (isRaise || isSee); // Constraint: Must be either bid or fold: isBid XOR isFold = 1
 hasChosen <-- isBid + isFold — 2*isBid*isFold;
 hasChosen === 1; // Constraint: numPairs must be > 0 if isBid = 1
 var hasPairs = (numPairs > 0);
 component not3 = NOT();
 not3.in <-- isBid; component or2 = OR();
 or2.a <-- hasPairs;
 or2.b <-- not3.out;
 or2.out === 1; out <-- or2.out;
```

## 运行证明

初始步骤在入门教程中已经很熟悉了:

```
circom poker.circom -o poker.json
snarkjs setup -o poker.json
```

下一步需要提供输入。让我们来看一个示例输入文件( *input.json* ):

```
{“cards”: [8, 7, 4, 7, 13], “isFold”: 0, “isSee”: 0, “raise”: 10 }
```

这手牌有一对，所以所有叫牌选项都可用。玩家选择加注 10。我们希望这个输入作为有效的输入被传递。手保持私有，而其他输入是公共的。我已经掩饰了玩家进入假手的可能性。也许可以通过散列这手牌来处理，公开散列，从而允许在这手牌结束时进行确认。在现实生活中的零知识证明中，程序的所有这些方面，包括电路代码和设置，都需要事先得到证明者和验证者的同意。验证者将坚持消除任何允许证明者伪造他们的手牌的可能性。

让我们来完成接下来的步骤，以生成证明:

```
snarkjs calculatewitness -c poker.json
snarkjs proof
```

证明生成性能将在 O(n)时间内运行，其中 n =约束的数量。在这个例子中，我们受到的约束太少，无法看到这种情况发生。见[此处](https://github.com/DalaiLlaama/snark-example/tree/master/factor-perftest)有更多约束的测试。

现在由验证者运行的验证:

```
PS C:\dev\snarks\poker> snarkjs verify
OK
```

…而且很有效！

有了精心设计的 ZK-斯纳克，信任链又回到了设置。为了从验证步骤中获得“OK”结果，证明者只能做一件事，那就是创建一个满足所有约束的见证。

我希望你觉得这是有用的。

## 完整的电路

```
include “../node_modules/circomlib/circuits/gates.circom”;
include “../node_modules/circomlib/circuits/comparators.circom”;template Poker() {
 signal private input cards[5]; // Each 2..14
 signal input isSee; // 1 or 0
 signal input raise; // int
 signal input isFold; // 1 or 0
 signal output out; // 1 or 0
 // Intermediate results
 signal isBid;
 signal isRaise;
 signal hasChosen; // Count pairs
 var numPairs = 0;
 for (var i=0; i<4; i++) {
    for (var j=i+1; j<5; j++) {
       if (cards[i] == cards[j]) {
          numPairs++;
          // break doesn’t work. Just force j and i to exit
          j = 5;
          i = 5;
       }
    }
 }
 // isRaise = (raise != 0)
 isRaise <--(raise > 0);
 isBid <--(isRaise || isSee);// Constraint: Must be either bid or fold: isBid XOR isFold = 1
 hasChosen <--isBid + isFold — 2*isBid*isFold;
 hasChosen === 1; // Constraint: numPairs must be > 0 if isBid = 1
 var hasPairs = (numPairs > 0);
 component not3 = NOT();
 not3.in <-- isBid; component or2 = OR();
 or2.a <-- hasPairs;
 or2.b <-- not3.out;
 or2.out === 1; out <-- or2.out;
}component main = Poker();
```

你可以在 [github](https://github.com/DalaiLlaama/snark-example/tree/master/poker) 中找到代码

[![](img/e7b1dbc6a532a697c6844fdf0f0bbd30.png)](https://cryptofi.co)

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)