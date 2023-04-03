# # how to guidl(2/n)::你的第一份合同

> 原文：<https://medium.com/coinmonks/howtobuidl-2ofn-come-one-come-all-8a3e0ee706b1?source=collection_archive---------4----------------------->

## 来吧来吧所有人！在这篇文章的最后，你将会写下你的第一份智能合同，名为“最伟大的表演”。

> 来一个！都来吧！来加入伟大的数字铁路吧！没有进入的障碍，只要你在被称为分散共识的规则框架内参与。这意味着你将为此而努力。这并不容易，但至少你已经迈出了第一步。来建造明天的数字铁路吧！一日三餐！你头上的屋顶！公平丰厚的工资！来，发财吧！不是每个人都会接受挑战。我想我会把它留给你。机会在等着你。

*如果还没有，一定要赶在阅读前* [*BUIDL :: Dev 环境设置*](/coinmonks/howtobuidl-series-1-of-n-bf51e248243d) *。*

![](img/70d868e93393677134daa4f0caafe78d.png)

Ethereum Blockchain lets us build the Digital Railroads of Tomorrow

## 就在此时此地。我提出了报价…

在# HowToBUIDL 系列的第一篇文章— [#BUIDL :: Dev 环境设置](/coinmonks/howtobuidl-series-1-of-n-bf51e248243d)中，我们介绍了如何为这一步准备好您的计算机环境。也许你仍然持怀疑态度，或者担心自己没有这个技能。你被困在自己的意识中，只是没有被指引方向。

所以把典型的东西换成多彩的。如果很疯狂，就活得疯狂一点。你可以明智地玩，传统之王，或者你可以冒险看看…

![](img/075e5121c0d176ab071f73f3b2d3cd8a.png)

这个通用以太坊区块链的核心是一个共识框架，允许任何人参与。该网络由一个激励网络来保证安全，在这个激励网络中，矿工可以获得被称为以太的加密安全货币作为奖励。一个点对点的节点网络保持了网络的健壮性，并允许人们向世界任何地方汇款，而不用考虑国界和微不足道的费用。

分散式平台的好处是大幅降低成本和准入门槛，消除单点故障，防止审查，并确保参与互动的所有各方之间的透明度和信任。但是这个空间不仅仅是资金转移。智能合同通过消除中介真正促进了这种去中心化的想法。

我们有方法将任何独特的对象表示为“令牌”,并且我们可以改变我们对所有权的看法。如果您拥有传输数据的权利，您就可以拥有传输数据所代表的内容的权利。在未来，我们可能会看到土地所有权、房地产所有权、融资协议和有限资源使用权的数字转移，其形式是应用程序公用令牌，甚至是可兑换成演出门票的老式门票。

***倾向于改变现状的不是最大的实体，而是最小的、往往是少数的声音，是变革的进步者*** *。在我看来，授权来自包容，这正是区块链式项目所代表的。没有人想被排除在市场之外，而一个集中的市场有可能被排除在外。区块链技术提供了一种方式来#建造数字铁路，以前所未有的方式连接人们。*

![](img/23cf8234e09d6e35f51765777188b1f6.png)

We’re going to the other side.

## 让我们开始吧。

智能契约是保持数据状态的构造，并为外部世界提供了一种与数据交互的方式。代码代表规则。也许“*聪明*”是用词不当。契约中的规则越复杂，就越难遵循，也就越难创建无 bug 的代码。事实上，你制定的契约越简单，你就越容易验证它，以确保代码做它说要做的事情。一旦部署到以太坊网络，代码是不可阻挡的，因此值得掌握合同中可以存储什么。虽然我们使用了术语“*合同”*但是目前，代码并没有在法庭上得到支持和执行。这些规则必须用代码定义，并专门编写来执行嵌入在契约中的逻辑。

每份合同都必须从描述最低版本的 *Solidity* 开始，这是一种用于编写以太坊智能合同的语言。禁止 0.4.22 及以下版本，[https://github.com/ethereum/solc-js/releases](https://github.com/ethereum/solc-js/releases)包含每个最新版本的信息。

首先创建一个名为 ComeOneComeAll 的新目录，启动 truffle。

```
mkdir ComeOneComeAll
cd ComeOneComeAll
truffle init
cd contracts/
npm install zeppelin-solidity
```

用你最喜欢的编辑器创建一个名为 GreatestShow.sol 的新文件
对于较大的项目，你可能想使用一个名为 VSCode 的编辑器，但我认为名为 Sublime 的免费编辑器对我们的目的来说很好。

```
pragma solidity ^0.4.23;
```

接下来，通过给它命名来开始契约。让我们建造最伟大的表演。当我们去部署 GreatestShow 契约时，将调用构造函数。让我们利用这个机会传递一些信息，并记录一些免费发送给我们的数据。我们会保留使用它的人的以太坊地址，也会记下那个领班。根据您的存储和成本需求，文本可以表示为`string`或`bytes`。合同中的每项操作，包括存储数据，都要花费一点钱，我们称之为“汽油”。现在让我们保持简单，使用一个字符串。

```
contract GreatestShow { address public owner public; 
    string public ringMaster; constructor(string _ringMaster) {
        owner = msg.sender;
    }}
```

如果我们要部署此合同，我们将在 migrations/2 _ deploy _ contracts . sol 文件中执行如下操作:

```
const GreatestShow = artifacts.require("./GreatestShow.sol");

module.exports = (deployer, network, accounts) => {
    deployer.deploy( GreatestShow, "P.T. Barnum" );
};
```

从命令行，我们可以运行:

```
truffle migrate
```

![](img/f6ae3eeb1be1bde7d70b89750de31b58.png)

G 太好了！我们有一场演出。从技术上来说，这就是我们达成明智合约所需要的一切。照这样，代码可以编译并部署到以太坊网络上。问题是，如果你只有一个马戏团团长，那就不算是一场表演。目前甚至没有任何参与者、演员或动物。在我们称我们的节目取得巨大成功之前，我们最好继续。

接下来，我们将向 GreatestShow 添加另一个属性，以便记录可用座位的总数。存储正数所需的变量类型是“无符号整数”，用 uint 表示。再说一次，现在只知道它是无符号的，因为它不可能是负的。这个数可以存储一个大到 2 的 256 次方的数(巨数！)因此，在未来，更好的做法是对存储持实际态度，只考虑与现实相关的数字。我们将添加一个函数，让合同的创建者存储可用座位的数量。

我们没有触及的一件事是变量的“公共”可访问性修饰符。出于数据安全的目的，假设合同中存储的所有数据都是公开可用的，即使没有标记。这是坚持公开、开放和抵制审查原则的好处之一。当一个变量被显式标记为 public 时，它会被赋予一个方便的访问器函数，默认情况下该函数的调用方可以使用该函数。因为我们在这里没有指定，所以有必要演示一下如何在`contract`的主体中实现 setter/getter 对。setter 接受一个我们分配给契约状态的参数，getter 注意到它是`returns(<type>)`。Solidity 函数可以返回多个值，但是在这种情况下，只返回一个值是有意义的。因为 getter 不会改变数据的状态，所以按照惯例，您应该将它标记为`view`。

```
 uint availableSeats; function setAvailableSeats( uint _availableSeats ) public {
        availableSeats = _availableSeats;
    } function getAvailableSeats() view public returns(uint) {
        return availableSeats;
    }
```

![](img/c2a53327249333f526cdef5f6367344a.png)

Awesome. We’ve got some seats and we’re ready to entertain!

没错。现在我们有了一个乐队指挥和座位，但是在我们有一整套表演来娱乐他们之前，没有人会被娱乐。

现在让我们在节目中加入一些表演。可以给一个表演起一个名字、表演中的演员数量、表演所需的老虎、熊和大象的数量以及分配的时间。此外，可能需要注意的是，表演开始前需要清理舞台。这将让节目的制作人知道，为了处理表演，需要一些特殊的准备。

所有的编程语言都试图通过给程序员提供通过某种数据结构定义对象的方法来模拟现实。在 Solidity 中，最常见的方法是使用`struct`——在这里可以定义用户定义的类型。在契约的主体中，让我们定义一个`Performance`结构。

```
 struct Performance {
        string name; uint32 actors; uint8 tigers;
        uint8 bears;
        uint8 elephants; uint16 timeInSeconds; bool clearTheStageFirst;
    }
```

契约的创建者现在需要一种方法，通过将所需的值传递给一个函数来添加一个`Performance`。在合同中存储这些数据是有成本的；但是，我们现在不讨论这个问题。现在，我们只是在演示跟踪它的必要性。这意味着我们将不得不跟踪`Performance`对象的`array`，标为`Performance[] performances`。我们将在每次调用`addPerformance`函数时追加到数组中。

```
 Performance[] performances;

  function addPerformance( 
              string _name,
              uint32 _actors,                              
              uint8 _tigers, 
              uint8 _bears,
              uint8 _elephants, 
              uint16 _timeInSeconds,
              bool _clearTheStageFirst ) public { performances.push( 
          Performance( { name: _name, actors: _actors, 
              tigers: _tigers, bears: _bears, 
              elephants: _elephants,
              timeInSeconds: _timeInSeconds,
              clearTheStageFirst: _clearTheStageFirst } 
          ) 
      );
  }
```

我们马上就要超越我们自己了，但希望这将有助于理清事情。在契约被部署到网络之后，契约的所有者可能会使用类似于下面的代码在`truffle console`中获得已部署契约的实例。

```
// Add a performance of Knife Throwing requiring 2 people. 
// The performance will last 3 minutes and require zero animals.
GreatestShow.deployed().then( function(show) {
    show.addPerformance( "Knife Thrower", 2, 0, 0, 0, 180, false );
} );
```

![](img/e162b35502e9cbe4a5df2a3656a7edaa.png)

```
// Add a performance of 4 Flame Breathers lasting 30 seconds
GreatestShow.deployed().then( function(show) {
    show.addPerformance( "Flame Breather", 4, 0, 0, 0, 30, false );
} );
```

![](img/3e321a5fe8a006c7acecfe724cc2baf3.png)

和大多数语言一样，Solidity 中的数组是零索引的。要获得合同代码中的第一个性能，您可以参考`performances[0]`。
但是，如果您作为合同的调用者想要访问特定性能的所有数据，该怎么办呢？想象一下，你的合同用户想要在某个 web 应用程序上列出信息。我们将添加一个方便的方法来获取传入的`_index`中特定`Performance`实例的所有数据。

请再次注意，数据没有改变，但也请注意，我们不能直接返回`Performance`对象。可靠性限制要求数据必须作为多个值返回，如`returns`语句所示。我们用括号`(`将所有返回值括起来`)`。

```
function getPerformance( uint _index ) view public 
      returns (string, uint32, uint8, uint8, uint8, uint16, bool) { Performance storage perf = performances[_index]; return (perf.name, perf.actors, perf.tigers, perf.bears, 
            perf.elephants, perf.timeInSeconds, 
            perf.clearTheStageFirst); 
  }
```

假设第三个条目是赞达亚扮演的空中飞人安妮惠勒。她是一个女人的表演，但需要一组 8 个其他配角来帮助抓住她，并在她表演各种杂技时把她扔来扔去。舞台需要对设备来说是清晰的。如何从表演中获取一些信息示例:

```
GreatestShow.deployed().then( function(show) {
   let performance = show.getPerformance( 2 ); // 0-indexed console.log( "Name:" + performance.name );     // Flying Trapeze
   console.log( "Actors:" + performance.actors ); // 9 = 1 + 8
   console.log( "Clear:" + performance.clearTheStageFirst); // true
});
```

![](img/14f17b82605dcc837a1b96f76c7567db.png)

Zendaya as Anne Wheeler the Flying Trapeze Artist and 8 supporting Actors

我们就要完成了，但是我们需要一个方法来为买票的人筹钱！幸运的是，在以太坊中有一种方法可以创建一个接受以太的函数，以太是代表数字货币的本地加密货币。以太对美元或英镑等普通法定货币的汇率是波动的，但我们不会因为争论而担心这一点。不，相反我们只会担心接受以太来交换一定数量的席位。

我们还没有讨论过`mapping`数据类型，但是本质上它允许我们将一个值映射到另一个值。在这里，我们将演示如何跟踪以太坊地址以及它们在无符号整数中的票数。

```
mapping(address => uint256) internal tickets;
```

接受以太的一个简单方法是将一个回退函数标记为`payable`，并在该函数内部执行逻辑，为函数调用方的以太坊地址分配一定数量的单位成本门票。一个简单的方法可能如下所示:

```
uint256 internal ethusd = 700; // peg to 700 dollars
uint256 internal rateInCents = 2000; // 2000 cents = $20.00function () payable public { uint256 _wei = msg.value; // 1 Ether = 1000000000000000000 wei
   uint256 tickets4ETH = _wei.mul(ethusd).mul(100).div(rateInCents);
   tickets[msg.sender] = tickets[msg.sender].add( tickets4ETH );}
```

暂且忽略 ETHUSD 利率在现实中是不断变化的。我们代表的固定价值是每张 700 美元，票价是 20 美元。可靠性有一些限制，不允许存储浮点数。我们知道我们必须处理这个限制，我们希望确保我们有更多的*位置可用于执行算术运算，以便我们在乘法或除法时不损失尽可能多的精度，因此我们首先乘以 100，然后将结果除以每张票的 rateInCents。我们也在依靠`SafeMath`图书馆来做`mul`、`div`、`add`以及`sub`。我们现在不讨论其中的原因，但请注意，智能合约中的任何算术运算都可能产生不良影响，有时还会出现灾难性的数据完整性问题，因此我们在执行数学运算时总是会检查溢出。*

回到控制台，安装 zeppelin-solidity:

```
npm install zeppelin-solidity
```

并且在你的文件顶部的`pragma`行之后&合同之前`GreatestShow`:

```
import 'zeppelin-solidity/contracts/math/SafeMath.sol';
```

然后，在实际合同中，第一行应该是:

```
using SafeMath for uint256; // adds helper functions to uint256
```

好的。我们准备好继续了！你已经创建了你的`GreatestShow`契约，你有办法指定你的`ringMaster`的名字，我们正在跟踪由`Performance`结构表示的`performances`数据结构，并保持`availableSeats`的数量。为了确保我们能够填补这些席位，我们确保`payable`回退功能可用，以便当客户从他们的帐户向我们发送乙醚时，我们根据他们发送给我们的数量向他们的`msg.sender`地址分配门票价值。

让我们回到控制台，准备好迎接奇迹的发生:

```
**truffle compile**Compiling ./contracts/GreatestShow.sol...Compiling zeppelin-solidity/contracts/math/SafeMath.sol...Writing artifacts to ./build/contracts
```

完全公开:我们故意在应用程序中留下了几个 bug，我们将在下一集解决它们，在下一集我们将研究如何测试我们的智能契约。代码编译不代表它*逻辑正确；*这仅仅意味着代码在语法上是正确的。对于智能合约，测试是必不可少的。过去，开发人员可以简单地停止服务器，恢复他们控制的数据库中的更改，并修补一个错误。但我们正处于一个新的范式中。[我们部署到区块链的代码是不可变的。](/coinmonks/stop-the-erc20-fud-just-learn-and-buidl-47871067362c)

## 你成功了！

恭喜你！您现在知道如何使用 Solidity 和 Truffle 框架#构建您的第一个基于以太坊区块链的智能合约！

> 别管笼子了，因为我们知道怎么做钥匙。
> 哦！该死，你突然可以自由飞翔了。我带你去另一边。

![](img/e03baf46be30f33c99d2f6b9a26efb13.png)

如果你被困住了，不要害怕。我有一份完整的项目副本，你可以从一个公共的 [GitHub 库](https://github.com/emmonspired/ComeOneComeAll/)下载，在那里你可以将你的代码与我已经检查过的工作示例进行比较。

Greatest Showman, 2017

*Dan Emmons 是*[*Emmons pired LLC*](http://www.emmonspired.com/)*的所有者，一名* [*认证比特币专业人士*](http://cryptoconsortium.org/lookup/6f0d14) *，认证以太坊开发者，全栈开发者，加密货币项目顾问。他还是一个名为*[*# ByteSizeBlockchain*](https://www.youtube.com/watch?v=SVBZ7mdgGcA)*的 Youtube 频道和 iTunes 播客的创作者。*

# 完整代码示例

**注意:**我们将在以后的文章中进行一些代码编辑(重构)。
这篇文章的目的是让你兴奋起来，激发你的想象力。在一篇文章中不一定能涵盖好的或专业的编程技术和代码风格的每个方面，我们也不想让你第一次尝试就不知所措。谢谢你远道而来。**继续前进！**

```
pragma solidity ^0.4.23;import 'zeppelin-solidity/contracts/math/SafeMath.sol';contract GreatestShow {

  address public owner;
  string public ringMaster; constructor(string _ringMaster) public {
    owner = msg.sender;
    ringMaster = _ringMaster;
  } uint availableSeats; function setAvailableSeats( uint _availableSeats ) public {
      availableSeats = _availableSeats;
  }

  function getAvailableSeats() view public returns(uint) {
      return availableSeats;
  } struct Performance {
    string name; uint32 actors; uint8 tigers;
    uint8 bears;
    uint8 elephants; uint16 timeInSeconds; bool clearTheStageFirst;
  } Performance[] performances;

  function addPerformance( 
              string _name,
              uint32 _actors,                              
              uint8 _tigers, 
              uint8 _bears,
              uint8 _elephants, 
              uint16 _timeInSeconds,
              bool _clearTheStageFirst ) public returns(uint) { performances.push( 
          Performance( { name: _name, actors: _actors, 
              tigers: _tigers, bears: _bears, 
              elephants: _elephants,
              timeInSeconds: _timeInSeconds,
              clearTheStageFirst: _clearTheStageFirst } 
          ) 
      ); return performances.length;
  }

  function getPerformance( uint _index ) view public 
      returns (string, uint32, uint8, uint8, uint8, uint16, bool) { Performance storage perf = performances[_index];
      return (perf.name, perf.actors, perf.tigers, perf.bears, 
       perf.elephants, perf.timeInSeconds, perf.clearTheStageFirst);
  }mapping(address => uint256) internal tickets;uint256 internal ethusd = 700; // peg to 700 dollars
uint256 internal rateInCents = 2000; // 2000 cents = $20.00using SafeMath for uint256; function () payable public {     
    uint256 _wei = msg.value; // 1 Ether = 1000000000000000000
    uint256 tickets4ETH =_wei.mul(ethusd).mul(100).div(rateInCents);
    tickets[msg.sender] = tickets[msg.sender].add( tickets4ETH );     
  }

}
```