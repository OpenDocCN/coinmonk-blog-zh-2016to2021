# 部署高效地址协定:演练

> 原文：<https://medium.com/coinmonks/deploy-an-efficient-address-contract-a-walkthrough-cb4be4ffbc70?source=collection_archive---------0----------------------->

![](img/87775bbfa0ba3676e2b279e30f51621c.png)

Because gas can get expensive!

你可能听说过节能以太坊合同地址应该是一个东西。如果不太麻烦的话，也许你甚至想在下一份合同中使用它。很可能，你根本不知道我在说什么。如果你属于后一类，我鼓励你[看看最后一个帖子](/@0age/on-efficient-ethereum-addresses-3fef0596e263)中所有可怕的细节——尽管之后你*仍然*可能不知道我到底在说什么。这里是它的要点:**具有额外零字节的地址通常使用**花费更少的汽油，并且有一种**更有效的方法来创建驻留在这些地址的契约**。

与其重复所有你可能想要使用一个有节能地址的合同的理由，不如让我们开始工作，学习如何部署你的下一个有节能地址的合同。根据您的特定使用情形、您的计算能力、您的技术敏锐度以及您的关系状态，您有六种选择:

1.  以通常的方式将协定部署到一个低效的地址。*(哈欠…)*
2.  通过 CREATE 以传统方式将合同部署到一个高效的地址。
3.  使用 **CREATE2 和一个工厂契约** *(比如巧妙命名的* `[Create2Factory](https://github.com/0age/Pr000xy/blob/master/contracts/Create2Factory.sol)` *)* 将契约部署到一个高效的地址。
4.  **将一个** [**可升级的透明代理**](https://github.com/zeppelinos/zos/blob/master/packages/lib/contracts/upgradeability/AdminUpgradeabilityProxy.sol) 部署到一个高效的地址，并使用 `[**Pr000xy**](https://github.com/0age/Pr000xy/blob/master/contracts/Pr000xy.sol)`将您的契约设置为实现**。**
5.  从朋友那里得到一些 `**Pr000xy**` **令牌**，用它们来申请一个可升级的透明代理，跳过这个过程中比较棘手的部分。
6.  记住这是你的周年纪念日，放下你正在做的一切，开始写一首十四行诗和烤纸杯蛋糕。

当谈到选项 1 和 6 时，你只能靠自己，所以我们将把注意力集中在选项 2 到 5 上。如果您还不习惯于部署智能合约和/或与智能合约直接交互，那么您可能不是接下来大部分内容的目标受众。让我们开始吧。

# OG 方法

自从地址出现以来，虚名地址就一直存在。他们更“令人难忘”或更“幸运”，在一个充斥着大肆宣传的江湖骗子和堕落赌徒的加密货币空间里，这两种特质都是可取的。以太坊名称服务着手解决“令人难忘”的部分，但仍然需要解决“幸运”的部分。出于这个原因，[消失了](https://github.com/Arachnid/vaniteth)创建了*(嗯，实际原因是为了帮助使 ENS 注册地址* ***本身*** *容易记忆，但是我离题了)*。简而言之，方法如下:

*   使用一些可靠的熵源生成私钥，并导出相关的地址。
*   在第一次执行多达 *N* 个事务后确定契约部署地址，其中 *N* 是您愿意使用的最高 nonce。
*   重复此操作，直到找到符合您要求的地址。
*   为该地址提供资金，并进行足够的虚拟交易，以将随机数发送到适当的地点。
*   最后，部署合同，享受你完成的技术壮举。

这种方法可以完成工作，并且在使用大值的 *N* 时会更快，但是有几个很大的缺点:

*   在部署合同时，你必须**格外小心，以获得恰到好处的随机数***，最终**浪费时间和精力**，因为你必须为地址提供资金，并进行一系列交易来增加随机数。*
*   *你必须**生成大量的私有密钥**，这降低了你遍历候选对象的速度，并且需要一个好的熵源，除非你想被黑客攻击。*
*   *即使你有很好的熵，如果有人在你的机器上安装了键盘记录器或者在采矿软件中安装了后门，或者可以通过其他方式窃取私钥，他们就可以窃取你的硬币和/或把 nonce 推得太高，以至于你无法将合同部署到正确的地址，那么你仍然有可能被黑客攻击。*(我还应该提到* [*分割密钥地址派生*](https://en.bitcoin.it/wiki/Split-key_vanity_address) *可以用来防御这种情况——但是，请记住，契约没有私钥，所以您仍然需要为地址提供资金，增加 nonce，并部署契约。)**

*所以，如果你有耐心，怀旧，伟大的 op-sec，和一个洞在你的口袋里，然后这个有你的名字在它上面！好消息是已经有高性能的库支持这个选项——看看 [profanity](https://github.com/johguse/profanity) 或 [ethaddrgen](https://github.com/Limeth/ethaddrgen) 就知道了。*

# *新来的人*

*随着君士坦丁堡*(现在在 Ropsten 上直播，预定在 mainnet 上 7，280，000 块)*的出现，有了一种新的部署契约的方式: [CREATE2](https://eips.ethereum.org/EIPS/eip-1014) 。在确定契约部署地址时，它不使用调用者的地址和随机数作为输入，而是使用**调用契约的地址**、一个 **32 字节 salt** 和正在部署的契约的**初始化代码**。与旧的现状相比，这有几个优点，因为它与虚荣心地址有关:*

*   *不需要生成新的密钥对，甚至不需要安全熵，因为 salt 可以递增*(只要小心避免冲突，就不会重复检查相同的 salt)* 。这提高了您遍历候选对象的速度，并防止由于缺乏高级加密和安全随机数生成知识而被黑客攻击。*
*   *你不需要为一个新的、潜在的 pwned 地址出资，也不需要浪费你的精力和理智:只要从你已经控制的账户发送一笔交易就可以了。*
*   ***攻击面以*的方式*向下**，你可信赖的私人密钥安然无恙地放在你的硬件钱包里——如果你愿意，你甚至可以从另一份已经存在的合同中将其踢开。(你用的是硬件钱包吧？)*

*这一切都很好，但有一个重要的警告*(总是有警告，该死！)*牢记在心。除了在事务中省略`to`字段并将初始化代码作为调用数据，您还需要通过一个 ***工厂契约*** 来为您调用 CREATE2。工厂合同最好对它将接受什么 salt 和来自谁有一些访问控制，否则你将容易受到抢先运行的攻击:当你提交 salt 时，监视事务池的人可以看到并抢先你一步。解决这个问题的一个好方法是让工厂契约**使用** `**msg.sender**` **作为 salt** 的前 20 个字节，剩下最后 12 个字节可用于查找所需的契约地址*(这仍然留给 2⁹⁶或每个调用者大约 79000 万亿个可能性)*。*

*让我们看看使用`Create2Factory`部署基本契约的步骤:*

*   ***获取您想要部署的契约的初始化代码**，然后**计算它的 32 字节 keccak-256 哈希**。*
*   ***在给定**初始化代码散列**和 **salt** 的情况下，确定`Create2Factory`将把合同部署到的地址**，并使用新的 salt 重复上述操作，直到找到符合您的规范的地址。*
*   ***通过调用`Create2Factory`并提供 **salt** 和**契约初始化代码**作为参数来部署契约**，记住**从 salt 开头编码的地址调用它。***

*相比之下很简单，是吧？这里有一种方法可以找到一个 salt，我们可以在 Ropsten 上给`[Create2Factory](https://ropsten.etherscan.io/address/0xa779284f095ef2ebb8ee26cd8384e49c57b26996)` [它将导致一个具有有效地址的契约。它使用了不起眼的](https://ropsten.etherscan.io/address/0xa779284f095ef2ebb8ee26cd8384e49c57b26996)`[**create2crunch**](https://github.com/0age/create2crunch)`地址矿工连同德高望重的[松露](https://github.com/trufflesuite/truffle) & [Web3.js v1.0](https://github.com/ethereum/web3.js/tree/1.0) 库*(安装* [*锈 v1.31.1*](https://rustup.rs/) *，* [*节点 v.11.4.0*](https://nodejs.org/en/download/) *，以及* [*纱*](https://yarnpkg.com/lang/en/docs/install) 【T31)*

```
*### Set up new project from scratch and install stuff.  [¬°-°]¬ ###
$ yarn global add truffle  # install truffle if you need to
$ mkdir myFirstEfficientAddress && cd myFirstEfficientAddress
$ truffle init
$ yarn init  # fill in the fields (or don't, I'm not your mother)
$ yarn add web3@1.0.0-beta.37### Behold: the example contract source code!  ⊹╰(⌣ʟ⌣)╯⊹ ###
$ cat >./contracts/ExampleContract.sol <<EOL
pragma solidity ^0.5.0;
contract ExampleContract {
  function exampleFunction() public pure returns (bool) {
    return true;
  }
}
EOL### Compile time!  ᕕ(⌐■_■)ᕗ ♪♬  ###
$ truffle compile### Create a little script for hashing init code.  ._.)/\(._. ###
$ cat >./getInitCodeHash.js <<EOL
var Web3 = require('web3')
const artifact = require('./build/contracts/ExampleContract.json')
const initCodeHash = Web3.utils.keccak256(artifact.bytecode)
console.log(initCodeHash)
EOL### Let's get the init code itself while we're at it.  ƪ(ړײ)‎ƪ ###
$ cat >./getInitCode.js <<EOL
const artifact = require('./build/contracts/ExampleContract.json')
console.log(artifact.bytecode)
EOL### Assign all the various arguments.  ᕕ( ᐛ )ᕗ ###
$ export FACTORY="0xa779284f095ef2eBb8ee26cd8384e49C57b26996"
$ export CALLER="<YOUR_ROPSTEN_ADDRESS_OF_CHOICE_GOES_HERE>"
$ export INIT_CODE=`node getInitCode.js`
$ export INIT_CODE_HASH=`node getInitCodeHash.js`### Install create2crunch and get crunching!  ༼つಠ益ಠ༽つ ─=≡ΣO)) ###
$ git clone [https://github.com/0age/create2crunch.git](https://github.com/0age/create2crunch.git)
$ cd create2crunch
$ cargo run --release $FACTORY $CALLER $INIT_CODE_HASH  # then ^c### Once you find one, go grab it. Hurray!  (๑•̀ㅂ•́)ง✧ ###
$ export SALT=`head -1 efficient_addresses.txt | cut -c1-66`### You'll need these for the deployment step.  (っˆڡˆς) ###
$ echo salt: $SALT
$ echo init code: $INIT_CODE
$ echo init code hash: $INIT_CODE_HASH*
```

**作为题外话，如果你感觉特别有野心，作为* `create2crunch` *的一部分，还有一个实验性的 GPU 选项。试试看！**

*手里拿着盐，是时候让奇迹发生了。有很多方法可以做到这一点，但我们将保持简单:[前往 MyEtherWallet (Ropsten 版本)](https://www.myetherwallet.com/#contracts)的合同部分，并进入`Create2Factory`合同地址:*

```
*0xa779284f095ef2eBb8ee26cd8384e49C57b26996*
```

*以及合同 ABI:*

```
*[{"constant":false,"inputs":[{"name":"salt","type":"bytes32"},{"name":"initializationCode","type":"bytes"}],"name":"callCreate2","outputs":[{"name":"deploymentAddress","type":"address"}],"payable":true,"stateMutability":"payable","type":"function"},{"constant":true,"inputs":[{"name":"salt","type":"bytes32"},{"name":"initCodeHash","type":"bytes32"}],"name":"findCreate2Address","outputs":[{"name":"deploymentAddress","type":"address"}],"payable":false,"stateMutability":"view","type":"function"}]*
```

*从这里开始，您可能想要通过调用`findCreate2Address`视图函数，传入 salt 和 init 代码散列来验证您是否得到了正确的地址。**确保 salt 的前 20 个字节与您的地址**匹配。一旦您满意了，使用`callCreate2`在有效地址部署契约，传入 salt 和契约初始化代码，并确保您将 gas 限制设置得足够高*(这是 Ropsten —全力以赴，挥金如土)*。就是这样！打电话给新合同上的`exampleFunction`并拿回那个甜蜜的、甜蜜的真布尔的奖励点。*

# *团队球员团结起来！*

*`Create2Factory`方法很棒，只要你有一个特定的、独特的合同要部署，并且愿意做所有必要的跑腿工作，自己为它找到一个有效的地址。但是，正如我祖父喜欢说的，“人多力量大。”*(如果他能超越“我在新闻上不断听到的‘比特币’是怎么回事”的范畴，我肯定他会喜欢这个类比。)*如果一群有效地址搜索者都选定了一个**共享工厂契约**、**初始化代码块**、和**每个有效地址的相对值**，他们就可以利用我们在上一篇文章中讨论的一大堆好东西，其中最主要的是**在他们努力达到有效地址天堂的过程中利用所有找到的**有效契约地址的能力。*

*`Pr000xy`通过使用**可升级的透明代理** *(在*[*ZeppelinOS*](https://zeppelinos.org/)*中使用的那些——你应该用* [*和*](https://docs.zeppelinos.org/docs/deploying.html) `[zos](https://docs.zeppelinos.org/docs/deploying.html)` *一起玩玩，如果你还没有的话，那里的工具非常整洁。每个人都部署同一个版本，然后，一旦它已经被部署，他们就可以通过将它指向具有所有期望逻辑的实现契约来设置它。它还在`Pr000xy`令牌中为每个契约地址赋予一个值，这赋予了一些受欢迎的**可替换性**。另外，您现在有一份可以升级的合同！当然，如果你有“代码就是法律”的话，你可以随时放弃这种能力笔记本外壳上的标签。)**

*请注意，在使用可升级的透明代理时，有一些额外的问题需要考虑。举几个例子:你只能从管理帐户调用管理方法*(改变管理或实现)*，你不能从管理帐户调用逻辑契约，你必须用初始化函数替换构造函数并记住调用它们，你必须小心不要在升级时无意中重用存储槽，以及如果逻辑契约遇到`selfdestruct`时你的代理的吐司。*

**为了详细说明最后一点，在处理通过* `CREATE2` *部署的合同时，有一种微妙的情况需要注意(更多上下文参见* [*本讨论*](https://gitter.im/ethereum/AllCoreDevs?at=5c5ca1c9f04ef00644f86b5f)*)****如果合同是*** `**selfdestruct**` ***ed，那么它可以被重新部署！*** *这意味着，如果有人最终破坏了您最初通过* `Pr000xy` *创建的合同，您可以再次部署它并获得两次相同的奖励。此外，在与任何具有* `selfdestruct` *操作码的* `CREATE2` *派生契约或可能导致它们* `selfdestruct` *的* `delegatecall` *派生契约进行交互时，要非常小心。显然，可升级代理属于后一类，* *因此* ***考虑对合同的升级管理员*** *设置适当的治理和其他控制。**

*记住这一点，让我们继续一个例子，从`[Pr000xy](https://github.com/0age/Pr000xy)` [Github repo](https://github.com/0age/Pr000xy) 开始工作，并使用提供的地址挖掘器 *(* `create2crunch` *)具有更好的性能，但我们在这里处于“测试模式”，您已经知道如何使用它了*:*

```
*### Pr000xy supplies the init code, so just supply the caller. ###
$ export CALLER="<YOUR_ROPSTEN_ADDRESS_OF_CHOICE_GOES_HERE>"### Pull down the repo, install dependencies, & build contracts. ###
$ git clone [https://github.com/0age/Pr000xy.git](https://github.com/0age/Pr000xy.git)
$ cd Pr000xy
$ yarn install
$ yarn build### Mine until you're satisfied & get salt - it's all you need. ###
$ yarn mine $CALLER  ## then ^c
$ export SALT=`head -1 valuableProxies.txt | cut -c1-66`
$ echo salt: $SALT*
```

*然后，我们进行与之前相同的练习:在 MyEtherWallet (Ropsten 版本)的[合同部分，提供`Pr000xy`合同地址:](https://www.myetherwallet.com/#contracts)*

```
*0x000000009a9fc3ac5280bA0D3eA852E57DD2ac1b*
```

*以及合同 ABI *(我知道，这里有很多***——我们可以改天再谈)*:**

```
**[{"constant":true,"inputs":[],"name":"name","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"value","type":"uint256"}],"name":"approve","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"initialized","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"totalSupply","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"from","type":"address"},{"name":"to","type":"address"},{"name":"value","type":"uint256"}],"name":"transferFrom","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"decimals","outputs":[{"name":"","type":"uint8"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"addedValue","type":"uint256"}],"name":"increaseAllowance","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"owner","type":"address"}],"name":"balanceOf","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"symbol","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"spender","type":"address"},{"name":"subtractedValue","type":"uint256"}],"name":"decreaseAllowance","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"to","type":"address"},{"name":"value","type":"uint256"}],"name":"transfer","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"owner","type":"address"},{"name":"spender","type":"address"}],"name":"allowance","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"inputs":[{"name":"rewardsRegistry","type":"address"},{"name":"proxyInitCode","type":"bytes"}],"payable":false,"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":true,"name":"creator","type":"address"},{"indexed":true,"name":"value","type":"uint256"},{"indexed":false,"name":"proxy","type":"address"}],"name":"ProxyCreated","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"claimant","type":"address"},{"indexed":true,"name":"value","type":"uint256"},{"indexed":false,"name":"proxy","type":"address"}],"name":"ProxyClaimed","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"creator","type":"address"},{"indexed":false,"name":"proxy","type":"address"}],"name":"ProxyCreatedAndClaimed","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"offerID","type":"uint256"},{"indexed":true,"name":"offerer","type":"address"},{"indexed":false,"name":"reward","type":"uint256"}],"name":"OfferCreated","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"offerID","type":"uint256"},{"indexed":true,"name":"offerer","type":"address"},{"indexed":true,"name":"submitter","type":"address"},{"indexed":false,"name":"reward","type":"uint256"}],"name":"OfferFulfilled","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"offerID","type":"uint256"},{"indexed":true,"name":"offerer","type":"address"},{"indexed":false,"name":"expiration","type":"uint256"}],"name":"OfferSetToExpire","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"from","type":"address"},{"indexed":true,"name":"to","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Transfer","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"owner","type":"address"},{"indexed":true,"name":"spender","type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Approval","type":"event"},{"constant":false,"inputs":[],"name":"initialize","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"salt","type":"bytes32"}],"name":"createProxy","outputs":[{"name":"proxy","type":"address"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"proxy","type":"address"},{"name":"owner","type":"address"},{"name":"implementation","type":"address"},{"name":"data","type":"bytes"}],"name":"claimProxy","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"leadingZeroBytes","type":"uint256"},{"name":"totalZeroBytes","type":"uint256"},{"name":"owner","type":"address"},{"name":"implementation","type":"address"},{"name":"data","type":"bytes"}],"name":"claimLatestProxy","outputs":[{"name":"proxy","type":"address"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"searchSpace","type":"bytes20"},{"name":"target","type":"address"},{"name":"recipient","type":"address"}],"name":"makeOffer","outputs":[{"name":"offerID","type":"uint256"}],"payable":true,"stateMutability":"payable","type":"function"},{"constant":false,"inputs":[{"name":"proxy","type":"address"},{"name":"offerID","type":"uint256"}],"name":"matchOffer","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"salt","type":"bytes32"},{"name":"offerID","type":"uint256"}],"name":"createAndMatch","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"salt","type":"bytes32"},{"name":"owner","type":"address"},{"name":"implementation","type":"address"},{"name":"data","type":"bytes"}],"name":"createAndClaim","outputs":[{"name":"proxy","type":"address"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"salts","type":"bytes32[]"}],"name":"batchCreate","outputs":[{"name":"proxies","type":"address[]"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[],"name":"batchCreateEfficient_H6KNX6","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"salts","type":"bytes32[]"},{"name":"offerIDs","type":"uint256[]"}],"name":"batchCreateAndMatch","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"offerID","type":"uint256"}],"name":"scheduleOfferExpiration","outputs":[{"name":"expiration","type":"uint256"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"offerID","type":"uint256"}],"name":"cancelOffer","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"salt","type":"bytes32"}],"name":"findProxyCreationAddress","outputs":[{"name":"proxy","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"proxy","type":"address"},{"name":"offerID","type":"uint256"}],"name":"matchesOffer","outputs":[{"name":"hasMatch","type":"bool"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"account","type":"address"}],"name":"getZeroBytes","outputs":[{"name":"leadingZeroBytes","type":"uint256"},{"name":"totalZeroBytes","type":"uint256"}],"payable":false,"stateMutability":"pure","type":"function"},{"constant":true,"inputs":[{"name":"leadingZeroBytes","type":"uint256"},{"name":"totalZeroBytes","type":"uint256"}],"name":"getValue","outputs":[{"name":"value","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"proxy","type":"address"}],"name":"getReward","outputs":[{"name":"value","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"leadingZeroBytes","type":"uint256"},{"name":"totalZeroBytes","type":"uint256"}],"name":"countProxiesAt","outputs":[{"name":"totalPertinentProxies","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"leadingZeroBytes","type":"uint256"},{"name":"totalZeroBytes","type":"uint256"},{"name":"index","type":"uint256"}],"name":"getProxyAt","outputs":[{"name":"proxy","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"countOffers","outputs":[{"name":"totalOffers","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"index","type":"uint256"}],"name":"getOfferID","outputs":[{"name":"offerID","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"offerID","type":"uint256"}],"name":"getOffer","outputs":[{"name":"amount","type":"uint256"},{"name":"expiration","type":"uint256"},{"name":"searchSpace","type":"bytes20"},{"name":"target","type":"address"},{"name":"offerer","type":"address"},{"name":"recipient","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"getInitialProxyImplementation","outputs":[{"name":"implementation","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"getProxyInitializationCode","outputs":[{"name":"initializationCode","type":"bytes"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"getProxyInitializationCodeHash","outputs":[{"name":"initializationCodeHash","type":"bytes32"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"proxy","type":"address"}],"name":"isAdmin","outputs":[{"name":"admin","type":"bool"}],"payable":false,"stateMutability":"view","type":"function"}]**
```

**然后，使用`findProxyCreationAddress`检查盐。从这里，你可以用你的盐通过`createProxy`铸造一些`Pr000xy`代币，或者你可以马上用`createAndClaim`为自己设置透明代理。我会为前者分配大约 700，000 gas，在后一种情况下，我会为逻辑契约上的`initialize`函数添加更多的*来说明。(开个玩笑，我知道你写的是简洁、模块化的合同。)*从这里开始，数字世界就是你的了！**

# *我的就是你的*

*如果你想跳过整个地址挖掘的折磨，你总是可以拿起一些`Pr000xy`令牌并烧掉它们，以获得一个已经创建好的透明代理。*

*首先，让我说清楚:如果`Pr000xy`在 mainnet 上上线，**不要从任何人那里购买** `**Pr000xy**` **令牌，除非你确实需要一个具有有效地址的可升级的透明代理，并打算尽快得到它！**更好的是，**我建议根本不要购买它们**——它们是高度实验性的、**高度通货紧缩的**象征，没有组织支持它们和**没有权利、股息或任何形式的利润预期**。 *(IANAL，但如果声称对加密货币拥有权威的众多监管机构之一对这个小实验感兴趣，我非常希望避免任何戏剧性事件，所以请原谅我的公司语言。)**

*话虽如此，以下是如何做到的(目前在 Ropsten 上):*

*   *获取一些测试令牌。如果你想要我寄一些给你，请联系我——你可以在网上找到我。)*
*   *在 MyEtherWallet (Ropsten 版本)的[合同部分填写`Pr000xy’s`地址`0x000000009a9fc3ac5280bA0D3eA852E57DD2ac1b`以及合同 ABI *(这是您必须滚动才能到达这里的又大又丑的代码块)*。](https://www.myetherwallet.com/#contracts)*
*   ***通过`getValue`计算出你能负担得起的**代理有多“罕见”,以检查前导零字节和总零字节的各种组合的特定值，或者`getReward`查看任意地址是如何赋值的。*(记住一个零字节实际上“看起来”像一对零。)**
*   ***通过首先调用`countProxiesAt`来检查具有您想要的前导字节数和总零字节数的代理是否可用，从而找到可用的代理**。*
*   *如果有一个可用的代理，**通过调用`claimLatestProxy`来声明最新的可用代理**，使用相同的参数表示前导字节和总零字节。您可以部署一个逻辑契约，并在您声明它时设置好所有东西，或者您可以将`owner`和`implementation`参数保留为空地址，将`data`参数保留为空，以便将所有权分配给您声明它的地址，并在事后处理所有设置。*

*现在你知道了——你知道如何部署那些地址已经减少了几磅的合同。如果你想参与进来，就开始摆弄`Create2Factory``Pr000xy`*(并且不要羞于在* [*电报频道*](https://t.me/joinchat/AAAAAEgfYEufIMuqEXFNQA) *)* 。现在，向前迈进，努力让你的合同在未来更有效率！*

> *[直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)*

*[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)*