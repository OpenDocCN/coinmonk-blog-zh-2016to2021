# 一对以太坊蜜罐契约的分析

> 原文：<https://medium.com/coinmonks/an-analysis-of-a-couple-ethereum-honeypot-contracts-5c07c95b0a8d?source=collection_archive---------2----------------------->

[以太扫描](https://etherscan.io)是一个以太坊区块链浏览器，除了其他功能外，它允许开发者提交他们部署的智能合约的代码。这个特性的主要好处是它允许用户通过阅读它们的源代码来检查契约做了什么。以太网扫描确保代码与部署的智能合同相匹配。

已核实的合同清单很长。在撰写本文时，Etherscan 提供了 26055 份合同的源代码，可以在这里[浏览](https://etherscan.io/contractsVerified)。

在一个慵懒的周日下午，我决定随便浏览一下，看看人们在运行什么样的合同，了解一下人们使用区块链的目的，以及这些合同写得有多好，有多安全。我发现的大多数合同都实现了代币、众卖、多签名钱包、庞氏骗局和..蜜罐！

蜜罐合同是我最感兴趣的发现。这种合同持有以太，而*假装*不安全地这样做。简而言之，它们是诈骗合同，试图欺骗你，让你认为你可以窃取他们持有的以太，而事实上你所能做的就是*失去*以太。

他们遵循的一个常见模式是，为了取回他们持有的以太，你必须先送他们一些你自己的以太。当然，如果你尝试这样做，你会大吃一惊:智能合约耗尽了你的精力，你会发现智能合约并不像你想象的那样。

在这篇文章中，我将分析我遇到的几个蜜罐合同，并解释它们看起来是做什么的，但实际上是做什么的。

## 不是真的不安全的非彩票

我将经历的第一个合同实现了一种彩票，显然，这种彩票非常不安全，而且很容易在保证中奖的情况下被窃取。我遇到过几个这样的例子。我找到的最后一个实例部署在地址 0x 8685631276 cfcf 17 a 973d 92 f 6 DC 11645 e 5158 c0c，其源代码可以在这里阅读[。为了方便起见，我复制了下面的代码。你能找到诱饵吗？你能告诉为什么，如果你试图利用它，你实际上会失去以太吗？](https://etherscan.io/address/0x8685631276cfcf17a973d92f6dc11645e5158c0c#code)

```
pragma solidity ^0.4.23;// CryptoRoulette
//
// Guess the number secretly stored in the blockchain and win the whole contract balance!
// A new number is randomly chosen after each try.
//
// To play, call the play() method with the guessed number (1-16).  Bet price: 0.2 ethercontract CryptoRoulette {
    uint256 private secretNumber;
    uint256 public lastPlayed;
    uint256 public betPrice = 0.001 ether;
    address public ownerAddr; struct Game {
        address player;
        uint256 number;
    }
    Game[] public gamesPlayed; constructor() public {
        ownerAddr = msg.sender;
        shuffle();
    } function shuffle() internal {
        // randomly set secretNumber with a value between 1 and 10
        secretNumber = 6;
    } function play(uint256 number) payable public {
        require(msg.value >= betPrice && number <= 10);
        Game game;
        game.player = msg.sender;
        game.number = number;
        gamesPlayed.push(game); if (number == secretNumber) {
            // win!
            msg.sender.transfer(this.balance);
        } //shuffle();
        lastPlayed = now;
    } function kill() public {
        if (msg.sender == ownerAddr && now > lastPlayed + 6 hours) {
            suicide(msg.sender);
        }
    } function() public payable { }
}
```

很容易看出`shuffle()`方法将`secretNumber`设置为 6。因此，如果你打电话给`play(6)`并发送 0.001 以太，你将总是赢得你的以太加上合同的余额，即 0.015 以太。轻松赚钱，对吧？不对。

有什么诀窍？仔细看看`play()`是如何实现的。它声明了一个变量`Game game`，但是没有初始化它。因此，它将默认为指向协定存储空间的槽 0 的指针。然后，它将您的地址存储在其第一个成员(存储槽 0)中，并将提交的号码存储在第二个成员(映射到存储槽 1)中。因此，在实践中，这将最终用攻击者账户的地址覆盖合同的`secretNumber`，用提交的号码覆盖`lastPlayed`。

然后，它会比较`secretNumber`，现在是你的帐户地址，与你提交的号码。由于您只能提交小于 10 的数字，因此只有您的帐户地址在 0x0 到 0x0a 的范围内，您才能获胜。(不要试图强行在这么小的范围内搜索一个帐户！根本不可行。)

所以，比较会失败，契约会保留你的以太。当然，攻击者可以随时调用`kill()`来取回以太。

## 不是真的不安全的非谜语

这是[另一个好玩的](https://etherscan.io/address/0x3CAF97B4D97276d75185aaF1DCf3A2A8755AFe27#codepragma)。这让我挠头了一会儿。然而，有一个巨大的漏洞，那就是这份合同马上就要搞出一些肮脏的事情。但是我们不要想太多。

这是它的代码。你能发现所谓的弱点吗？您能告诉我为什么漏洞利用不起作用吗？我刚才说的赠品是什么？

```
contract G_GAME
{
    function Play(string _response)
    external
    payable
    {
        require(msg.sender == tx.origin);
        if(responseHash == keccak256(_response) && msg.value>1 ether)
        {
            msg.sender.transfer(this.balance);
        }
    }

    string public question;
    address questionSender;
    bytes32 responseHash;

    function StartGame(string _question,string _response)
    public
    payable
    {
        if(responseHash==0x0)
        {
            responseHash = keccak256(_response);
            question = _question;
            questionSender = msg.sender;
        }
    }

    function StopGame()
    public
    payable
    {
       require(msg.sender==questionSender);
       msg.sender.transfer(this.balance);
    }

    function NewQuestion(string _question, bytes32 _responseHash)
    public
    payable
    {
        require(msg.sender==questionSender);
        question = _question;
        responseHash = _responseHash;
    }

    function() public payable{}
}
```

该代码据说实现了一个谜语。它提出了一个问题，如果你能说出答案是什么，它大概会把它的余额发给你，目前是 1 以太多一点。当然，要产生一个答案，你必须先发送一个以太，如果你是正确的，你会得到它。代码看起来不错，但是有一个卑鄙的伎俩:注意`NewQuestion`如何允许`questionSender`提交一个与`_question`不匹配的散列。所以，只要这个函数没有被使用，我们应该没问题。

我们能说出问题和答案是什么吗？如果您在 etherscan 上阅读合同的[交易历史，似乎第](https://etherscan.io/address/0x3CAF97B4D97276d75185aaF1DCf3A2A8755AFe27)[次交易](https://etherscan.io/tx/0xa31f023b306fd48facbf5ce54a9c5690edbf1ad90caa676785f431ae49a1ab69)提出了问题。如果你点击 etherscan 上的“转换成 UT8”按钮就更明显了。这就揭示了问题"*我很容易进入，却很难走出我。我是什么？*”，以及答案*麻烦*。

根据 etherscan，由于该事务被调用，因此在创建合同后，`responseHash`将为零，并将变为`keccak265("TroublE")`。然后，有第三个事务装载了契约中的一个以太。所以，很明显，我们可以打电话给`Play("TroublE")`，派一个以太去取回两个以太。好得难以置信？大概吧。让我们确认一下。

我们可以通过检查智能合约的状态来确保我们将合约的以太网。它的变量不是`public`，但是仍然只需要几个额外的笔划就可以通过查询区块链来检索它们的值。`questionSender`和`responseHash`是第二个和第三个变量，因此它们将占用智能合约存储空间上的槽 1 和槽 2。让我们检索它们的值。

```
web3.eth.getStorageAt(‘0x3caf97b4d97276d75185aaf1dcf3a2a8755afe27’, 1, console.log);
```

结果是` 0x0..0765951 ab 946 F3 a6f 0379680 a6b 05 FB 807d 52 ba 09 `。这对攻击者来说意味着麻烦(双关语),因为设置问题的交易来自一个以`0x21d2`开头的账户。出事了。

```
web3.eth.getStorageAt(‘0x3caf97b4d97276d75185aaf1dcf3a2a8755afe27’, 2, console.log);
```

结果是` 0xc3fa7df9bf24…`。这是“麻烦”的散列吗？

```
web3.sha3('TroublE');
```

该调用返回`0x92a930d5...`，因此结果是，如果我们调用`Play("TroublE")`并发送 1 ether，我们实际上会丢失它。但是哈希值怎么可能不匹配呢？

请注意，如果`responseHash`已经设置，那么`StartGame`什么也不做。显然，第二个事务没有改变契约的状态，所以它一定是在这个事务之前就已经设置好了。但是如果这是合同创建后的第一个事务，那么`responseHash`怎么可能已经初始化了呢？

在绞尽脑汁之后，我发现了最近一篇关于蜜罐合约的有趣帖子，解释了当 T0 为零时，Etherscan 不会显示合约之间的交易。其他区块链探险者如以太链*做*展示他们。毫无疑问，etherchain 揭示了合同历史中的[几个额外的交易](https://www.etherchain.org/account/3caf97b4d97276d75185aaf1dcf3a2a8755afe27)，其中在`0x765951..`的合同通过一个零值交易修改了`responseHash`。

所以让我们检查一下这些交易。也许以太还能被偷？为了追踪发生了什么，我们需要解码这些电话。我们可以从 Etherscan 得到合约的 ABI [，从 Etherchain 的“奇偶痕迹”得到内部交易数据(](https://etherscan.io/address/0x3CAF97B4D97276d75185aaF1DCf3A2A8755AFe27#code)[第一](https://www.etherchain.org/tx/4581ff1f1242b21c16f4f8bf0b0de19153c95d1461b9105543629d005e18c956/parityTrace)、[第二](http://a5b368a448ae104747db475797aa6157e5db0729e3d90f46c0250061596cccf7))。这就是我们将交易解码为人类可读格式所需的全部内容。

```
const abiDecoder = require('abi-decoder');
const Web3 = require('web3');
const web3 = new Web3();const abi = [{“constant”:false,”inputs”:[{“name”:”_question”,”type”:”string”},{“name”:”_response”,”type”:”string”}],”name”:”StartGame”,”outputs”:[],”payable”:true,”stateMutability”:”payable”,”type”:”function”},{“constant”:false,”inputs”:[{“name”:”_question”,”type”:”string”},{“name”:”_responseHash”,”type”:”bytes32"}],”name”:”NewQuestion”,”outputs”:[],”payable”:true,”stateMutability”:”payable”,”type”:”function”},{“constant”:true,”inputs”:[],”name”:”question”,”outputs”:[{“name”:””,”type”:”string”}],”payable”:false,”stateMutability”:”view”,”type”:”function”},{“constant”:false,”inputs”:[{“name”:”_response”,”type”:”string”}],”name”:”Play”,”outputs”:[],”payable”:true,”stateMutability”:”payable”,”type”:”function”},{“constant”:false,”inputs”:[],”name”:”StopGame”,”outputs”:[],”payable”:true,”stateMutability”:”payable”,”type”:”function”},{“payable”:true,”stateMutability”:”payable”,”type”:”fallback”}];const data1 = '0x1f1c827f000000000000000000000000000000000000000000000000000000000000004000000000000000000000000000000000000000000000000000000000000000c000000000000000000000000000000000000000000000000000000000000000464920616d2076657279206561737920746f2067657420696e746f2c627574206974206973206861726420746f20676574206f7574206f66206d652e205768617420616d20493f0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000754726f75626c4500000000000000000000000000000000000000000000000000';const data2 = '0x3e3ee8590000000000000000000000000000000000000000000000000000000000000040c3fa7df9bf247d144f6933776e672e599a5ed406cd0a15a9f2da09055b8f906700000000000000000000000000000000000000000000000000000000000000464920616d2076657279206561737920746f2067657420696e746f2c627574206974206973206861726420746f20676574206f7574206f66206d652e205768617420616d20493f0000000000000000000000000000000000000000000000000000';abiDecoder.addABI(abi);
console.log(abiDecoder.decodeMethod(data1));
console.log(abiDecoder.decodeMethod(data2));
```

运行这段代码，我们得到以下结果:

```
{ name: ‘StartGame’,
  params: [ { name: ‘_question’,
              value: ‘I am very easy to get into,but it is hard to get out of me. What am I?’,
              type: ‘string’ },
            { name: ‘_response’,
              value: ‘TroublE’,
              type: ‘string’ }
  ]
}
{ name: ‘NewQuestion’,
  params: [ { name: ‘_question’,
              value: ‘I am very easy to get into,but it is hard to get out of me. What am I?’,
              type: ‘string’ },
            { name: ‘_responseHash’,
              value: ‘0xc3fa7df9bf247d144f6933776e672e599a5ed406cd0a15a9f2da09055b8f9067’,
              type: ‘bytes32’ }
  ]
}
```

我们了解到第一个事务将答案设置为`keccak256("TroublE")`，但是第二个事务将答案设置为一个哈希值，我们不知道这个哈希值的原始数据！同样，很容易忽略第二个调用没有使用`_question`来计算散列；相反，它被设置为一个任意值，该值与前面调用中提供的字符串不匹配，尽管问题匹配。

所以，除非我们能找到产生给定散列的值，可能通过字典攻击或暴力搜索，否则我们就没运气了。而且，考虑到这个蜜罐有多复杂，我认为尝试暴力破解对我们来说不会有太好的效果。

解开这个蜜罐需要相当大的努力。它的创建者最终指望攻击者信任以太扫描数据，这些数据并不包含全貌。

## 赠品

我说这份合同包含了一个它的创造者在耍花招的确凿证据。这是这条线:

```
require(msg.sender == tx.origin);
```

这一行实现的是，它防止契约调用`Play`。这是因为`tx.origin`始终是一个“外部账户”，而不是一个智能合约。为什么这对攻击者有用？一种安全攻击契约的方法是从“攻击契约”中调用它们，如果它没有从攻击中获得以太网，则恢复执行:

```
function attack() {
    uint intialBalance = this.balance;
    attack_contract();
    require (this.balance > initialBalance);
}
```

这样，除非攻击者的合同余额增加，否则交易完全失败。蜜罐的创造者希望防止攻击者使用这种伎俩来保护自己。

## 结论

蜜罐对我来说是一个道德灰色地带。欺骗那些想从合同中偷窃的人可以吗？我不这么认为。但是我对此没有强烈的感觉。最后，如果你被骗了，那是因为你一开始就在寻找可以窃取的智能合约。

这些骗局利用了一些人的贪婪，这些人足够聪明，能够发现合同中明显的漏洞，但却没有足够的知识来发现潜在的陷阱是什么。

如果你想更深入地了解智能合约安全，可以看看这个叫做[占领以太](https://capturetheether.com/)的神奇战争游戏。这是一种锻炼你的技能和训练你的眼睛发现可疑代码的有趣方式。