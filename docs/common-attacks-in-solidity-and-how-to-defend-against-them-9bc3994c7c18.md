# Solidity 中常见的攻击及其防御方法

> 原文：<https://medium.com/coinmonks/common-attacks-in-solidity-and-how-to-defend-against-them-9bc3994c7c18?source=collection_archive---------0----------------------->

![](img/a89292c8f4c3573f2ba3af3aaaa25e75.png)

“black and gray laptop computer turned on” by [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

你的心沉了下去。你的呼吸变得如此缓慢，你似乎屏住了呼吸。当你面对眼前屏幕上闪烁的数字时，你睁大了眼睛，感到恶心:你的智能合同被黑了。不知何故。

让我们不惜一切代价避免这种情况，好吗？让我们意识到所有已知的攻击类型，并教会自己如何避免它们，这样我们就可以在晚上睡得很香。

# 资源

在我们深入研究之前，我 ***高度，高度*** 建议通过浏览 [OpenZeppelin 的 ether aut](https://ethernaut.zeppelin.solutions/)来补充您在本文中所学的内容，这是一个“游戏”，您可以打开浏览器控制台，使用注入的 web3 对象对 Ropsten 测试网络进行 RPC(远程过程调用)来攻击易受攻击的合同，以便在游戏中前进。它甚至让你去 [Remix IDE](https://remix.ethereum.org/) 编写并部署恶意契约给 Ropsten，这样你就可以调用那些契约中的自定义函数来利用 Ethernaut 契约中的漏洞。

这个产品非常出色，我认为它是任何有抱负的或当前的智能合约开发人员的强制实践。

# 竞争条件:可重入性

```
mapping (address => uint) private userBalances;

function withdrawBalance() public {
    uint amountToWithdraw = userBalances[msg.sender];
    msg.sender.transfer(amountToWithdraw);
    userBalances[msg.sender] = 0;
}
```

在上面的函数中，请注意乙醚发送给用户后`userBalances[msg.sender]`是如何设置为 0 的。如果这个函数被快速调用多次(比如用一个脚本，或者通过创建和部署你自己的恶意契约来调用这个函数，然后递归地调用它自己)，在它被设置为 0 之前，有可能发送`userBalances[msg.sender]`多次。这种类型的利用就是 2016 年 [DAO 被黑的原因](/swlh/the-story-of-the-dao-its-history-and-consequences-71e6a8a551ee)(导致 ETH 和 ETC 的社区分叉)。

避免这种情况的最好方法是简单地将`msg.sender.transfer(amountToWithdraw)`线移动到`userBalances[msg.sender] = 0`线以下，以便在余额归零后转移资金。“转账失败怎么办？”你问。那么，该功能将被还原，以便`userBalances[msg.sender]`不会被错误地设置为`0`。

# 竞争条件:跨函数状态依赖

```
mapping (address => uint) private userBalances;

function transfer(address _recipient, uint _amount) {
    require(userBalances[msg.sender] >= _amount);
    userBalances[_recipient] += _amount;
    userBalances[msg.sender] -= _amount;
}

function withdrawBalance() public {
    uint amountToWithdraw = userBalances[msg.sender];
    msg.sender.transfer(amountToWithdraw);
    userBalances[msg.sender] = 0;
}
```

类似于第一个竞争条件漏洞，如果您有两个依赖于相同契约状态的函数，那么在另一个函数执行的中途调用一个函数并得到不希望的结果是可能的。以上，可以同时调用两个函数，导致`userBalances[recipient] += amount`(第一个函数)关闭，然后在第二个函数中`msg.sender.transfer(amountToWithdraw)`关闭，然后在第一个函数中`userBalances[msg.sender] -= amount`关闭。这将导致用户能够将钱发送到另一个地址(他们可能也拥有这个地址)，然后为自己提取相同的金额(从合同余额中窃取)。

同样，防御这种情况的方法与第一场比赛相同:**在转移乙醚之前一定要改变你的状态**。

# 整数溢出/下溢

在上面的示例中，您是否看到了其他易受攻击的地方？如果黑客成功地将他们所有的钱转移到了一个二级账户，那么`userBalances[msg.sender] -= amount`会在`userBalances[msg.sender]`可能没有很多钱的时候被触发，然后`userBalances[msg.sender]`会下溢。

当`uint`递增/递减超过极限时，发生上溢/下溢。`uint`(是`uint256`的别名)的最大值是 2 ^ 256–1。如果一个整数的增量超过这个数字，它将溢出，并且该值将返回到 0。所有的`uint`尺寸都是如此，例如`uint8`，它明显更小(最大尺寸= 2 ^ 8 - 1 = 255)。溢出的示例:

```
uint8 myNumber = 255;

function increment(uint _number) public returns (uint) {
    _number += _number;
    return _number;
}

uint newNumber = increment(myNumber); // newNumber is now 0, not 256
```

这也可能发生在流不足的情况下。任何降到零度以下的`uint`都会跳到它的最大值。你能想象看到某个合同的用户`uint balance = 0`跳到`1.157920892E77 — 1`时的震惊吗？！？

为了防止这种情况，使用 [SafeMath](https://ethereumdev.io/safemath-protect-overflows/) 库(由 OpenZeppelin 的不可思议的人构建)，它可以恢复任何被调用的防止溢出的函数。迫不及待地想让这个功能成为实实在在的标准！

# 合同余额相关性

您知道吗，当第一次部署一个契约时，完全可以预先计算该契约的地址，然后在部署该契约之前将以太网发送到该地址。？**永远不要假设您的合同部署时余额为零**。

你也可以强行发送以太到任何合同，影响它的总平衡。请注意，即使您有一个回退功能是*而不是* `payable`:

```
contract exampleContract {
    function() { } // The fallback function
}
```

…您仍然可以向合同发送以太网！怎么会？！？你不能用**钱包地址**(即你的个人 ETH 账户)来做，但是你*可以用**合同地址**来做，就像这样:*

```
contract etherBomb {
    address recipient; // First, send this contract some ether
    // Then, call the below function to set a recipient address function setTarget(address _target) public {
        recipient = _target;
    } // Then call this function to destroy this contract
    // and forcibly send the funds anywhere function kill() public { 
        selfdestruct(recipient);
    }
}
```

因为所有这些事情，无论何时在你的应用程序逻辑中处理合同平衡，特别是在 `if` **或** `require` **语句**中，**都要非常小心。**

# 拒绝服务

我在之前的漏洞中提到过这一点，但还是值得重复一下:**并不是所有与你的契约交互的地址都会是钱包地址——其他契约也可以调用你的代码**。正因为如此，它们可以用来破坏你的应用程序。请考虑以下情况:

```
contract Auction {
    address highestBidder;
    uint highestBid; function bid() public {
        require(msg.value < highestBid);        if (highestBidder != 0x0) { 
            highestBidder.transfer(highestBid); // Return old bid
        } // Store new highest bid and bidder
        highestBid = msg.value; 
        highestBidder = msg.sender;
    }
}
```

如果有这样的恶意合同:

```
contract AuctionAPI {
    function bid(); // Exposes function after contract instantiation
}contract Attacker {
    address auctionAddress = 0x0123456789abcdef; // Put address here
    AuctionAPI auctionContract = AuctionAPI(auctionAddress); function placeBid(uint _amount) public {
        auctionContract.bid.value(_amount); 
    } function () payable {
        throw; // Rejects the payment before the function completes
    }
}
```

假设其中有以太，那么`Attacker`契约可以通过调用`bid()`函数将其以太发送给`Auction`契约，并成为`highestBidder`。因为当一个新的最高出价进来时,`Auction`合同将旧的最高出价返回给它的所有者，当一个新的最高出价进来时，它将试图把钱送回给`Attacker`。但是由于`Attacker`有一个抛出的回退功能，它拒绝支付并将其发送回`Auction`，并且`Auction`在设置新的最高出价/投标人之前停止`bid`功能。实际上，这使合同瘫痪了。

防范这种情况的一个好方法是实现一个**“平衡撤销”设计模式**(下面将详细介绍)，这样任何以太网传输功能都独立于其他应用程序逻辑。

# 时间戳/矿工篡改

矿工能够在一个区块内对交易进行排序。这是 dEX 领域中一个众所周知的问题，称为“前向运行”，在这种情况下，同一个块中的某些买入/卖出订单可能会被重新安排，以不同于预期的顺序执行。此外，矿工还能够调整块时间戳(在大约 30 秒的范围内)，因此**使用** `block.timestamp` **从来都不是好的做法——使用** `block.number` **而不是**。如果你必须使用一个时间戳，并且它的准确性对你的应用程序至关重要，考虑从一个有真实性证明的甲骨文中提取信息，或者考虑[以太坊闹钟](https://www.ethereum-alarm-clock.com/)。要了解为什么以太坊中的时间戳是一个问题，请查看(并投票支持)这个[精彩的 StackExchange 答案](https://ethereum.stackexchange.com/questions/15047/solidity-timestamp-dependency-is-it-possible-to-do-safely/15054#15054?newreg=2caa8a26114d4125984bdf83b854ef08)。

或者，如果情况需要，你可以实现一个**“提交/揭示”设计模式**(更多内容见下文)，如果让世界能够看到即将被挖掘的事务的数据是有问题的。

# 委派电话

根据[固化文件](https://solidity.readthedocs.io/en/v0.4.21/introduction-to-smart-contracts.html#delegatecall-callcode-and-libraries):

> 消息调用有一个特殊的变体，名为 **delegatecall** ，除了目标地址的代码在调用契约的上下文中执行以及`msg.sender`和`msg.value`不改变它们的值之外，它与消息调用相同。
> 
> 这意味着契约可以在运行时从不同的地址动态加载代码。存储、当前地址和余额仍然引用调用合同，只是代码取自被调用地址。
> 
> 这使得在 Solidity 中实现“库”功能成为可能:可重复使用的库代码，可应用于合同的存储，例如，为了实现复杂的数据结构。

围绕该功能使用的不当安全是 2017 年[奇偶校验 Multisig 钱包黑客](https://blog.zeppelin.solutions/on-the-parity-wallet-multisig-hack-405a8c12e8f7)的原因。TL；DR 该函数对于授予您的合同调用其他合同的能力非常有用，就好像该代码是您自己的合同的一部分一样。然而，使用`delegatecall()` **会导致被调用契约中的所有公共函数都可以被任何人**调用——在使用公共库时，这通常不是问题。但是因为这种行为在奇偶校验构建自己的定制库时没有被识别…

1.  在被调用的库中放置了不适当的安全措施。
2.  这允许黑客通过将他/她自己的地址作为多重签名授权者来重新初始化钱包。
3.  现在拥有了所有的 multisig 授权者地址，黑客就能够取出所有的合同资金。

为了更深入地了解这一场景，请查看[这篇令人惊叹的文章](https://blog.zeppelin.solutions/on-the-parity-wallet-multisig-hack-405a8c12e8f7)。

要点:当您将代码组织到多个契约、库和包中时，在依赖`delegatecall()`时要非常小心——您必须有完整的端到端安全措施来防止不必要的存储修改。

![](img/be8b0c5820b3d0fc8063b67527481014.png)

“bald eagle door chain lock” by [MILKOVÍ](https://unsplash.com/@milkovi?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 一般合同写作最佳实践

如果您坚持编写可靠、干净的代码，引入漏洞的风险会更小。这些好习惯应该成为你的第二天性。

## 功能/数据结构暴露

最佳实践是明确说明每个函数和数据结构的权限级别，以便完全了解钱包地址或其他协定地址可以看到或调用哪些函数和数据。如果你不熟悉`external`、`internal`、`public`和`private`之间的区别，请查阅[可靠性文档](https://solidity.readthedocs.io/en/v0.4.24/types.html?highlight=public#function-types)。

## 合同所有权

明智的做法是使用 [OpenZeppelin 的](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/ownership/Ownable.sol) `[Ownable](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/ownership/Ownable.sol)` [包](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/ownership/Ownable.sol)建立一个拥有管理权限并可以调用所有者特定函数的单一地址。这非常有用，尤其是下面的“断路器”功能。

## 断路器

```
bool public contractPaused = false;function circuitBreaker() public onlyOwner { // onlyOwner can call
    if (contractPaused == false) { contractPaused = true; }
    else { contractPaused = false; }
}// If the contract is paused, stop the modified function
// Attach this modifier to all public functions
modifier checkIfPaused() {
    require(contractPaused == false);
    _;
}
```

明智的做法是实现一个“断路器”功能修改器，在灾难性事件发生时阻止所有应用程序功能。此功能应与`Ownable`配合使用，以便只有合同所有者可以暂停/取消暂停应用程序。这有助于在危急情况下争取时间，并分析出了什么问题以及如何解决。尽管一些用户可能会抗议这种方法缺乏分散性，但在部署后的某个时候，您可以简单地将契约所有权转移到`0x0`地址，防止任何人调用`Ownable`功能。

## 环

```
struct Account {
    address userAddress;
    uint balance;
}Account[] private accounts;function withdrawAllBalances() public {
    for (uint i = 0; i < accounts.length; i++) {
        Account storage user = accounts[i];
        user.userAddress.transfer(user.balance);
        user.balance = 0;
    }
}
```

编写循环时要非常小心，因为它们会很快达到块气体限制，尤其是在动态大小的数据结构(如地址数组，可以向该数组添加更多地址)上的循环。

## 整理传入的数据/函数参数

您的 DApp 前端不是唯一可以发送您的合同 RPC 的地方，所以您必须在将任何传入的数据或函数参数插入存储之前对它们进行净化。永远不要相信合同之外的任何东西。

## 早点失败，大声失败

```
function usesIfStatements() {
    if (msg.sender == owner) { // Will fail silently
        doSomething();
    }
}function usesRequire() {
    require(msg.sender == owner); // Will fail loudly
    doSomething();
}
```

只要有可能，总是支持`require()`语句而不是`if`语句，因为如果不满足`require()`语句，它将抛出一个`revert`并发送给函数调用方(如前端)，并立即停止函数。`if`语句往往会无声无息地失败，还会导致更难阅读的深层嵌套代码。

尽可能将这些语句放在函数的顶部，以便尽早检查这些条件，从而节省开销并确保不会执行不需要的功能。

![](img/14ca9c39e897260879d697b4c08a8c05.png)

“empty gray concrete pathway with roof” by [Alex J. Reyes](https://unsplash.com/@next_spideey21?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 有用的设计模式

通过在您的代码中实现可靠的设计模式，可以减少上述许多漏洞。下面是几个有用的。

## 余额提取(又名“拉”与“推”支付)

```
mapping (address => uint) private balances;function depositFunds() public {
    balances[msg.sender] += msg.value;
}function checkBalance() public returns (uint) {
    return balances[msg.sender];
}function withdrawFunds(uint _amount) public {
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] -= _amount; // Reverts if transfer fails // This is what we want separated from other app logic
    msg.sender.transfer(_amount); 
}function sendFunds(address _receiver, uint _amount) public {
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] -= _amount;
    balances[_receiver] += _amount;
}
```

这种设计模式将发送以太网的动作与所有其他应用程序功能分开。您可以跟踪所有用户的余额，允许他们在合同中存入/提取资金，允许他们用这些资金做合同中的事情(而不实际将以太送出合同)，只有当以太实际被送出合同时，您才应该使用`.transfer()`，它应该在自己的功能中，以防传输失败。

## 依赖国家的行动(又名[“国家机器”](https://solidity.readthedocs.io/en/develop/common-patterns.html#state-machine))

```
struct Order internal {
    string from;
    string to;
    uint price;
    uint Status;
}mapping (uint => Order) public orders; // orderIdenum Status {
    Open,
    Cancelled,
    AwaitingShipping,
    InTransit,
    Delivered,
    Complete
}function markAsDelivered(uint _orderId) public {
    require(orders[_orderId].Status == Status.InTransit);
    orders[_orderId].Status = Status.Delivered;
}
```

一个非常有用的设计模式是“状态机”，这是一种限制采取某些动作的方法，除非满足某个状态条件。在上面的运输示例中，订单不能被标记为已交付，除非其当前状态是`InTransit`。设置这些条件可以确保步骤的逻辑性和可执行性。这种设计模式允许在你的应用程序中实现难以置信的复杂性——这是目前我最喜欢的！

## 提交/展示

当一个用户的行为或选择可能会影响另一个用户的行为或选择时，例如投票，或者游戏中所有玩家的行为同时被显示的行为，或者甚至是一次秘密拍卖，最好使用提交/显示设计模式。基本上，你把 DApp 分成两个独立的阶段(用一个“状态机”！):提交阶段和揭示阶段。

1.  在提交阶段，用户提交他们的 move/vote/etc 的加密散列，它存储在契约中。没有人通过查看合同存储知道实际的移动/投票/等等是什么，因为他们只看到一个散列。 ***更新*** *:感谢* [*罗马风暴*](https://medium.com/u/6477e735faed?source=post_page-----9bc3994c7c18--------------------------------) *指出您应该在前端使用* `*web3.utils.soliditySha3()*` *对值* ***进行加密，然后在*** *将它们传输到区块链(而不是将原始的、未加密的、暴露的数据接收到合同中，然后合同对其进行加密)，因为那些交易中的数据是公开的！*
2.  在所有用户提交之后，我们进入展示阶段。用户必须重新提交他们的原始移动/投票/等，以证明，通过散列它，它匹配他们最初提交的。这将验证该操作。

要了解关于这种设计模式的更多信息，请查看这篇[精彩文章](https://karl.tech/learning-solidity-part-2-voting/)。如果你想看这个代码的例子，可以看看我的[去中心化石头剪刀布游戏](https://github.com/joelsfoster/rock-paper-scissors)。

## 减速带/动作节流

```
mapping (address => uint) private userLastAction;
uint throttleTime = 30 seconds; // Attach this to critical functions, such as balance withdrawals
modifier speedBump() {      
    if (!userLastAction[msg.sender]) {
        userLastAction[msg.sender] = 0;
    } require(now - throttleTime >= userLastAction[msg.sender]);
    userLastAction[msg.sender] = now; // now == block.timestamp
    _;
}
```

这种设计模式可以用来限制用户操作。是的，因为储存成本，这要消耗很多汽油。但是在那些只采取一些非常关键的动作的应用程序中，这种设计模式是有益的。上面，出于说明的目的，我使用了`now`又名`block.timestamp`，尽管用`block.number`代替`uint throttleTime = 1; // 1 block`会更好。

## 自动折旧

```
uint public expirationBlock = 7654321;// Attach this modifier to all public functions
modifier preventIfDeprecated() {
    require(expirationBlock >= block.number);
    _;
}
```

这种模式对于在特定时间(比如 alpha/beta 测试期间)关闭合同很有用，因为这样就不必使用`selfdestruct()`，也不会丢失合同的所有存储数据。

## 数据分离

如果您希望建立可升级的合同，您应该将所有应用程序数据保存在一个单独的合同中，并建立允许哪些地址(您的 DApp 的最新版本)修改该合同状态的条件。这是相对高级的，我不会在这里深入探讨，但如果你想知道更多，请查看这个[基本代码片段](https://github.com/cjgdev/smart-contract-patterns/blob/master/maintenance/data_segregation.sol)和这个[高级文章](/aigang-network/upgradable-smart-contracts-what-weve-learned-at-aigang-b181d3d4b668)和这个[无中生有的文章](https://blog.colony.io/writing-upgradeable-contracts-in-solidity-6743f0eecc88)。如果你真的想深入细节，可以看看这篇关于以太坊的文章。

# 信用

我从 [Consensys 学院开发者项目](https://consensys.net/academy/2018developer/)中学到了很多这方面的信息。这篇文章中的一些代码片段直接受到了他们在课程中给我们展示的例子的启发(尽管*没有完全抄袭】。*

如果你在这篇文章中发现了什么需要改正的地方，请给我留言并告诉我！如果您的项目正在寻找兼职或合同智能合同开发人员，请联系我！我为合适的项目提供开发和审计服务，以及产品管理服务。

*我在 OMGpool.org*[](http://omgpool.org)**管理产品/战略/业务开发，我们正在那里为 OmiseGO 建立第一个开源的、社区驱动的赌注池。我也写智能合同——看看我在 github.com/joelsfoster.的表现**

**请务必在我们的网站上查看我们的赌注奖励* [*计算器*](http://omgpool.org/staking-rewards-calculator) *和* [*新闻提要*](http://omgpool.org/news) *，并在等待赌注到来的同时关注我们正在添加的新功能。**

**如果您想在赌注池启动时第一个听到消息，请在下面注册接收电子邮件更新:**

**关注我们上*[*Twitter*](https://twitter.com/omg_pool)*|*[*Medium*](https://medium.com/omgpool)*|*[*Telegram*](https://t.me/joinchat/HNsNKU4uI9ZavKNgGr2HjQ)*|*[*Reddit*](https://www.reddit.com/r/OMGPool/)*

*给我们发电子邮件:contact@omgpool.org*

> *加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)*

## *另外，阅读*

*   *[复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)*
*   *[网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)*
*   *[密码电报信号](/coinmonks/top-3-telegram-channels-for-crypto-traders-in-2021-8385f4411ff4) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)*
*   *[币安交易机器人](/coinmonks/binance-trading-bots-d0d57bb62c4c) | [OKEx 评论](/coinmonks/okex-review-6b369304110f) | [阿塔尼评论](https://coincodecap.com/atani-review)*
*   *[最佳加密交易信号电报](/coinmonks/best-crypto-signals-telegram-5785cdbc4b2b) | [MoonXBT 评论](/coinmonks/moonxbt-review-6e4ab26d037)*
*   *[如何在 Bitbns 上购买柴犬(SHIB)币？](https://coincodecap.com/buy-shiba-bitbns) | [购买弗洛基](https://coincodecap.com/buy-floki-inu-token)*
*   *[CoinFLEX 评论](https://coincodecap.com/coinflex-review) | [AEX 交易所评论](https://coincodecap.com/aex-exchange-review) | [UPbit 评论](https://coincodecap.com/upbit-review)*
*   *[十大最佳加密货币博客](https://coincodecap.com/best-cryptocurrency-blogs) | [YouHodler 评论](https://coincodecap.com/youhodler-review)*
*   *[AscendEx 保证金交易](https://coincodecap.com/ascendex-margin-trading) | [Bitfinex 赌注](https://coincodecap.com/bitfinex-staking)*
*   *[最好的卡达诺钱包](https://coincodecap.com/best-cardano-wallets) | [Bingbon 副本交易](https://coincodecap.com/bingbon-copy-trading)*
*   *[最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)*
*   *[开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)*
*   *最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)*
*   *[免费加密信号](/coinmonks/free-crypto-signals-48b25e61a8da) | [加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)*
*   *[杠杆代币的终极指南](/coinmonks/leveraged-token-3f5257808b22)*