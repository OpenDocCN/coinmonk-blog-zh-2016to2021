# 比较现代智能订约环境

> 原文：<https://medium.com/coinmonks/comparing-modern-smart-contracting-environments-d821304b96ef?source=collection_archive---------2----------------------->

![](img/bad56ede6c390e34f033b704e63cef72.png)

Photo by [Florian Olivo](https://unsplash.com/@florianolv?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 用坚实、近、墨书写智能合同！、索拉纳和科斯瓦斯姆

# 放弃

自从写了这篇文章，我就加入了 NEAR 团队，开发他们的 SDK 和其他 Rust 工具。在写这篇文章的时候，我与文章中的任何生态系统都没有联系，并且我不会基于来自任何生态系统的新信息来更新文章，以试图获得收集到的信息的更公正的快照。

# 为什么选择智能合约？

智能合约可以被认为是运行在区块链上的程序。在分散的区块链上运行代码可以创造什么价值？当你创建一个标准合同时，你会依靠经纪人和律师来创建和处理争议。在这种情况下，你必须支付大笔费用，并信任第三方来确保协议得到执行。现在，让我们将它与程序化智能合约进行比较。假设区块链的 VM 中没有错误，不仅任何人都可以用代码创建契约，而且解释契约的结果也应该总是确定的。这对企业来说非常重要，因为他们可以在一个不可信和确定的环境中削减成本和时间。

智能合约越来越受欢迎和使用，因为这些系统是无权限的。这一点的价值在 [DeFi](https://en.wikipedia.org/wiki/Decentralized_finance) 中表现得最为明显，因为它允许任何人创造金融工具，也允许任何人使用它们。这方面的一个很好的例子是 [Aave](https://aave.com/) ，这是一个令牌借贷协议，允许任何用户通过向借贷池提供流动性来赚取利息，以抵押品的形式借用令牌，或获得快速贷款(要求在交易结束时偿还贷款)。所有这些功能都比必须通过银行更快，更不受限制，银行至少强制你有一个银行账户，你的信用足够好，并且你通过了批准过程。

智能合约的替代方案是特定于应用的区块链。这些是独立的链，具有自定义逻辑和自己的验证器，通过其共识机制和验证逻辑，允许更可配置的安全性。开发专用区块链有两个主要框架，分别是 [Substrate](https://substrate.dev) 和 [Cosmos-SDK](https://github.com/cosmos/cosmos-sdk) 。

重要的是不要将特定于应用程序的区块链与智能合约混为一谈，因为它们有特定的用例及缺点。智能合约存在一些限制，即它们仅在向其发送交易时执行，它们受到运行它们的虚拟机或环境的限制，并且网络参数无法配置为适合每个应用的需求。

尽管特定于应用程序的区块链可以解决这些问题，但它也有一些明显的缺点，例如:

*   在应用程序启动之前，需要有一个围绕它的社区和生态系统，以确保足够的验证器和安全性让用户信任
*   与部署智能合同相比，启动区块链需要更多的经验和开发成本
*   这些应用程序在当前状态下是不可组合的。今天存在的跨链通信协议处于非常原始的状态，需要大量的开销， [IBC](https://ibcprotocol.org/) 刚刚在 2021 年 2 月中旬推出了[宇宙-SDK 星际之门升级](https://figment.io/resources/cosmos-stargate-upgrade-overview/#ibc)(尽管该协议是网络不可知的)，而 [XCMP](https://wiki.polkadot.network/docs/en/learn-crosschain) ，它是 Polkadot 的等价物，仍在开发中。

智能合约或应用程序与其他智能合约直接交互的能力允许它们用作构建块，以避免重复和协同工作。

由于智能合同可以更快、更容易地用作去中心化应用程序的构建模块，并且进入门槛最低，因此它们使个人能够轻松地创造价值，并为与其交互的任何人拓展可能性。

虽然以太坊已经主导了智能签约领域，并且在不久的将来仍将占据主导地位，但还有一些非常有趣且可扩展的替代方案。在撰写本文时，通过 [Uniswap](https://uniswap.org/) 进行一次简单的 ERC20 转账大约需要 19 美元，或者进行一次代币交易大约需要 58 美元，这对于普通消费者来说是不可行的。当对财务要求设置了更高的标准时，无许可或廉价的论点就没有那么重要了，这就是为什么本文将介绍和比较备选方案。

# 本文将涵盖的内容

这篇文章将主要集中在开发人员的体验上。、CosmWasm 和 Solana。每种方法都包括以下内容:

*   协议概述
*   初始化新合同
*   可用的环境设置和工具
*   关于智能合同语言和运行代码的虚拟机的技术细节
*   智能合同如何适应协议
*   编写合同逻辑和测试功能

# 这篇文章不会涉及的内容

这主要是以开发者为中心的，因此不会涉及任何协议的经济性。

比较将在构建和测试每个中的智能契约时结束，不包括在链上部署契约、从前端与之交互或升级契约代码(对于支持的协议)。代码中的跨契约调用也不会被涵盖，但是我将描述它们在必要时如何交互。

# **将建立什么样的合同进行比较？**

对于合同逻辑，我决定写一个[财阀](https://www.dictionary.com/browse/plutocratic)托管合同，任何人都可以购买一个路由，并将他们的内容放在链上(在本例中，是一个字符串)。这个想法只是每个路线的最高出价者将能够显示他们选择的任何内容，这将在未来通过前端加载。

我选择使用这种任意逻辑来比较不同的协议，因为它涵盖了您可以在大多数智能订约环境中进行的以下常见交互:

*   合同的初始化
*   从合同中检索数据的查询(获取内容)
*   向合同和变更状态发送令牌(购买途径)
*   从合同转移令牌
*   访问环境变量，如发送者地址、在事务中发送的值以及契约中锁定的令牌

# 固态

Solidity 是一种智能的合同语言，可以编译成 [EVM](https://ethereum.org/en/developers/docs/evm) 字节码。这是迄今为止使用最多的智能合同语言。大多数网络为 EVM 提供支持。为了将此与本文中涉及的协议联系起来，所有四个网络目前都有或正在努力实现 EVM 支持:

*   [以太薄荷(宇宙)](https://github.com/cosmos/ethermint)
*   [基板 EVM 托盘(Polkadot)](https://substrate.dev/docs/en/knowledgebase/smart-contracts/evm-pallet)
*   [EVM 附近](https://github.com/near/near-evm)
*   [Solana(非常早期的阶段，不开源)](https://solana.com/en/ecosystem/velas)

这些选项并不意味着是长期的解决方案，但有趣的是，即使是最近和最现代的网络也在努力实现 EVM 支持，以支持新开发人员的加入和现有应用的迁移。为任何 EVM 的连锁店或有 EVM 支持的连锁店建立合同的开发 UX 将是非常相似的，但是对于这个部分，将假设代码将被部署在以太坊上。

总部位于 EVM 的连锁店使用的两种主要语言是 [Solidity](https://soliditylang.org) 和 [Vyper](https://vyper.readthedocs.io/en/latest/index.html) ，还有 [Yul](https://docs.soliditylang.org/en/v0.5.8/yul.html) 和 [Yul+](https://github.com/FuelLabs/yulp) 这是中间语言选项。我选择用 Solidity 来创建这个例子，因为它是迄今为止这些选项中使用最多的语言。

在开发智能合约时，坚固性可能不是最现代或最普遍可用的语言，但鉴于以太坊和 EVM 连锁店在生态系统中的主导地位，如果你想尽快创造价值，坚固性将是可以使用的语言。鉴于受欢迎程度，您还将获得最好的开发人员工具和钱包支持。由于 Solidity 只能编译成 EVM 字节码，它将会过时，因为大多数现代解决方案正在远离 EVM(并且有很好的理由)。

对于本文，我们将使用 [Truffle](https://www.trufflesuite.com/truffle) 作为编译和测试契约的框架。我将注意到，对于开发，我使用了 [Remix](https://remix.ethereum.org/) ，因为它是开发 Solidity smart contracts 的事实上的 IDE，并使编写和测试像这样的快速项目更容易。

这里有一个快速入门的例子。

现在，让我们看看合同是什么样的，然后再看一些重要的部分。这个例子的代码可以在[这里](https://github.com/austinabell/smart-contracts-compare/tree/main/solidity)找到。

本合同的第一部分描述了将存储在链上的数据以及存储的格式。ContentRecord 结构定义了为每个路由存储的值。这

```
mapping(string => ContentRecord) values; 
```

描述将有一些键值存储，其中字符串 key 表示一个路由，ContentRecord 值包含该路由的内容以及所有者信息。最后，与合同相关联的最后一段数据是 contractOwner，地址被标记为 payable，因为资金可以发送到这个地址。

接下来要讲的是函数修饰符，这可能看起来不太熟悉:

```
modifier onlyOwner { require(msg.sender == contractOwner, "Sender is not the contract owner"); _;}
```

[函数修饰符](https://www.tutorialspoint.com/solidity/solidity_function_modifiers.htm)可用于用一些额外的验证或逻辑来包装函数，这些可以作为属性添加到任何函数中。在这种情况下，修改器在执行函数之前检查消息发送者是否是契约所有者。_；语法只是指示应用该属性的函数逻辑将在哪里执行。

这些功能和逻辑非常简单，但要注意其中的几个要点:

*   购买功能要求应付款修改量表明它可以接收随交易转移的价值
*   在查找映射时，任何不存在的键都将索引一个零值，没有空指针或可选返回的概念
*   withdraw 函数应用我们上面创建的 onlyOwner 修饰符，只允许合同所有者从合同中撤销。合同里没有用到资金，这种情况下把所有东西都转出就可以了。
*   getRoute 函数应用 view 修饰符来表示它不会修改任何状态。这个函数还指定它返回内存中的字符串内容。

松露测试位于[这里](https://github.com/austinabell/smart-contracts-compare/blob/main/solidity/test/contentTrackerTest.js)，可以用

```
truffle test
```

默认情况下，Truffle 测试框架将启动一个开发区块链，并预先分配要在测试中使用的帐户。这很好，因为您可以进行全面的集成测试，与实际区块链上的交互相匹配。这些测试将通过[以太坊 JSON-RPC API](https://ethereum.org/en/developers/docs/apis/json-rpc/) 与指定的网络进行交互。几乎所有与链和合同的交互都要通过这个 API，不管是好是坏。因为一切都要通过这个 API，所以你甚至可以选择把后端换成任何你喜欢的支持以太坊 RPC-API 的网络(我在测试我在以太坊上的工作时就是这么做的)。

太棒了，我们现在已经完成了合同！现在继续其他的比较(我保证下一个不会涉及)。

# 近的

附近是一个股权和区块链的证据。NEAR 的 VM 执行通过他们的 [Rust SDK](https://github.com/near/near-sdk-rs) 和 [AssemblyScript SDK](https://github.com/near/near-sdk-as) 生成的 Wasm 代码。对于那些不熟悉的人来说， [Rust](https://www.rust-lang.org) 通常被用来生成 Wasm 代码，因为它有极好的支持，是一种内存安全的语言，并且非常高效，开销很低。AssemblyScript 也常用于 Wasm，因为它是 TypeScript 的一种变体，而且非常高效。他们建议使用 Rust 来获得更好的 UX，但在这种情况下同时支持两者是很好的，因为其他 Wasm VM 区块链通常只支持 Rust，它允许不熟悉 Rust 的人有一个不太陡峭的学习曲线。

我已经在 GitHub 上设置了契约的 [Rust](https://github.com/austinabell/smart-contracts-compare/tree/main/near/contracts/rust) 和 [AssemblyScript](https://github.com/austinabell/smart-contracts-compare/tree/main/near/contracts/assembly) 变体，但是在本文中我们将只讨论 Rust 契约，因为它最容易与其他契约进行比较。

NEAR 的分片设计允许它水平扩展，而不是通过增加验证器硬件需求来限制垂直扩展。他们的分片协议被命名为[茄影](https://near.org/papers/nightshade/#nightshade)，与其他分片区块链的主要区别在于，有一个链管理每个块中发生的所有事务，而一个分片的验证器只需维护和验证该给定分片中修改的事务和状态。就开发人员的体验而言，分片的通常缺点是协议很难保持原子性，因为一个分片上的一个块可能是孤立的，而事务的另一部分已经提交给另一个分片链。Nightshade 旨在简化和解决与此相关的问题以及存储/状态膨胀，这是一个非常常见的瓶颈。

NEAR 的跨契约调用都假设契约不在同一个片上(以避免契约共存于同一个片上，从而避免拥塞和优势)，这意味着所有调用都将是异步的。尽管这增加了复杂性，但它也允许并行地与多个契约进行交互，而不必同步地等待每个契约的响应。因为跨契约交互目前有很多开销，并且可以跨越多个块，所以这对于提高单个用户的效率非常有用。

NEAR 的碎片目前计划是同构的，这意味着每个碎片将有相同的运行时来执行事务和相同的验证逻辑。这对于智能签约环境通常是必要的，并且还允许动态重新分级。在当前的协议设计中，没有一个选项可以创建一个链/碎片来适应大型应用程序的利益相关者，这是 Cosmos 和 Polkadot 明确设计来支持的一点。

这里附近[有几个入门的例子。](https://examples.near.org)

将被比较的合同的所有代码位于[这里](https://github.com/austinabell/smart-contracts-compare/blob/main/near/contracts/rust/src/lib.rs)。现在来看实际的合同代码！

忽略一些粗糙的边缘，这是非常干净的！现在，让我们浏览一下合同的一些细节，并把它与可靠性对等物联系起来:

*   用#[near_bindgen]注释的 ContentTracker 定义了契约及其状态
*   ContentTracker 的默认实现相当于 Solidity 中的构造函数，在部署契约时调用。
*   购买功能用#[应付款]标注，相当于实付属性
*   令牌传递，带有承诺::new(…)。转移(…)；行是上面提到的异步调用的例子。
*   传输是预定的，但是合同没有等待结果来完成购买路线
*   数据结构 LookupMap <string contentrecord="">处理访问存储的键值查找，大致相当于 Solidity 中的映射</string>

如果您不熟悉 Rust 语法，可能会有一些令人困惑的代码部分，但是希望现在功能已经很清楚了。测试这个契约就像运行 yarn test:cargo in near 目录一样简单。所有测试都位于与契约相同的文件[中，并且可以使用 testing_env 来调用函数！宏来初始化环境变量。还有一种方法可以通过初始化 JS 客户机并使用 jest 框架(或任何等效框架)来进行完整的 e2e 测试，但是我认为这超出了本文的讨论范围。](https://github.com/austinabell/smart-contracts-compare/blob/main/near/contracts/rust/src/lib.rs)

# 墨水！

[墨迹！](https://paritytech.github.io/ink-docs/)是编译成 Wasm 的奇偶校验智能合约语言。这是如何融入更大的 Polkadot 生态系统的，你可以将这些智能合同部署到任何 [Parachain](https://wiki.polkadot.network/docs/en/learn-parachains) ，大致相当于一个碎片，它支持内置的合同托盘。社区中对此的计划存在不确定性，因此细节将是模糊的。广义而言，智能合约是生态系统的一个较小的组成部分，并不固有地包含在网络中。我选择接下来讨论这个问题，因为它类似于 NEAR，因为它有一个共享的安全模型，并且以某种方式被分片，但是它不同于 NEAR，因为 Parachains 是异构的，这意味着它们有自己的状态转换函数，该函数也被编译为 Wasm 并包含在中继链中。

应用程序配置状态转换逻辑以满足其利益相关者需求的能力是一个很好的特性，但是它也伴随着通过链上拍卖获得 [Parachain 插槽的不确定性。我不会详细讨论 Parachain，但请注意，部署代码的方法是使用](https://wiki.polkadot.network/docs/en/learn-auction) [Substrate](https://substrate.dev/) 构建 Parachain/Parathread，使用 ink 构建智能契约！(我们将在接下来介绍)，或者将基于 EVM 的合同部署到支持 EVM 货盘的副链。

Parachain 运行时和智能合约编译到 Wasm 的好处是，网络可以升级，而无需引入硬分叉。这些无分支升级有助于消除节点运营商所需的开销，这也允许更快速的更新。

部署到同一个 Parachain 的所有智能合约可以彼此同步交互。不幸的是，前面提到的 XCMP 协议仍在开发中。沟通仅限于合同碎片上的东西。不幸的是，这意味着，如果智能合约在可组合方面有好处，那么智能合约从网络的分片结构中获得的好处将非常少。作为这个生态系统中的智能合同开发人员，您需要确保有支持合同托盘的副链，并根据所需的网络参数以及也将部署在那里的项目生态系统来选择一个副链。网络可以灵活地代表网络的利益相关者，但它在很大程度上控制着副链槽的所有者，这可能更不稳定，更难预测。

[这里](https://substrate.dev/substrate-contracts-workshop/#/0/building-your-contract)是墨迹入门教程！而合同代码，可以在[这里](https://github.com/austinabell/smart-contracts-compare/blob/main/ink/lib.rs)找到，是这样的:

与近月合约相比，这是非常相似的，但有几点需要注意:

*   封装在模块中的合同代码
*   使用#[ink(event)]属性易于生成事件，并且能够使用#[ink(topic)]索引日志
*   是的，其他协议也有记录的能力，但具体来说，定义和索引特定字段数据的便利性在这里是非常清晰的
*   与特定退出代码相关的错误
*   能够表达从存储中延迟加载哪些值，以及内置存储类型的默认延迟加载和缓存
*   所有类型都非常准确地从 std 库中移植过来，这使得熟悉 Rust 的任何人都可以非常容易地使用它

经历了建造这个的过程后，我想给建造这个的团队一个大大的击掌，他们真的做得很棒！不熟悉 Rust 的人可能会对一些粗糙的边缘有一些问题，但总的来说，这是一个很好的环境设置。他们有一个优秀的开发人员 UX，因为它易于阅读，具有良好的错误处理，简单而有用的日志记录，优秀的文档，惯用的 Rust 代码，并且非常优化。

至于测试代码，所有的测试都是使用 Rust 的测试框架在模块中编写的，并与 cargo contract test 一起运行。这些只是模拟 env 设置的单元测试，要进行完整的 e2e 测试，您需要自己手动部署和测试契约。有希望的是，该工具在未来会扩展到能够在测试框架内运行测试节点，从而使集成测试更容易重现，并且开销更少。

# CosmWasm

下一个智能签约平台是 [CosmWasm](https://docs.cosmwasm.com) 。CosmWasm 是一个 [Cosmos-SDK](https://github.com/cosmos/cosmos-sdk) 模块，类似于 Polkadot 中的契约托盘，它允许您将 Wasm 智能契约支持添加到任何用 Cosmos-SDK 构建的链中。Cosmos 的网络拓扑不同于其他方案，因为默认情况下，它没有共享的安全模型，也没有分层结构，而是被分成独立的区域，这些区域处理自己的验证器集。Cosmos-SDK 旨在创建特定于应用程序的区块链，其中应用程序之间的互操作性是通过 [IBC](https://ibcprotocol.org/) 实现的。这创建了一个环境，其中状态机可以很好地匹配应用程序/网络的利益相关者，但它带来了必须为网络装载可信验证器、启动链(比部署契约开销更大)的成本，并且考虑到开销和 IBC 的不稳定性质，可组合性更差。

虽然 CosmWasm 可以插入任何链中，但用于 testnets 的链是基于 [wasmd](https://github.com/CosmWasm/wasmd) 的，它是 Gaia(Cosmos hub 链)的一个分支。目前还没有为此启动的 mainnet。

CosmWasm 是对 Cosmos 空间的一个很好的补充，与开发和启动 Cosmos zone 相比，它允许智能签约，并允许开发人员构建更小、更可组合的应用程序。另一点需要指出的是，在构建 Cosmos zone 时，你需要使用 [Go](https://golang.org/) 作为编程语言，或者在 [Tendermint](https://docs.tendermint.com/master) 之上构建应用程序时，你需要手动满足 [ABCI 接口](https://docs.tendermint.com/master/spec/abci/)。CosmWasm 确实向 Rust 开发人员敞开了大门，同时也缩小了创建分散式应用程序时需要构建的内容的范围。

关于与 CosmWasm 的可组合性，有趣的一点是同步契约调用[被明确禁止](https://docs.cosmwasm.com/0.13/architecture/smart-contracts.html#avoiding-reentrancy-attacks)以避免重入攻击，并且可以在当前事务失败后将执行的事务返回中指定消息。这使得某些交互更加困难，特别是当您依赖调用另一个契约的结果来完成某个状态时，但是不允许的动机是合理的。

至于代码，如果您曾经编写过 Cosmos-SDK 模块，它会看起来非常熟悉。CosmWasm 的教程可以在[这里](https://docs.cosmwasm.com/0.13/getting-started/intro.html)找到，这个合同的代码位于[这里](https://github.com/austinabell/smart-contracts-compare/tree/main/cosmwasm)。

首先要注意的是 cosmwasm/src/state.rs 中与状态的交互，这些是帮助与状态交互的包装函数。

这定义了读取特定于契约的状态(在本例中是 config)和键到值的映射。这可以与 Cosmos-SDK 应用程序的管理员相比较。

接下来，在 cosmwasm/src/error.rs 中定义错误:

消息类型定义保存在 cosmwasm/src/msg.rs 中:

其中 HandleMsg enum 定义了状态转换逻辑的类型(Cosmos-SDK 中的 msg ), query msg 处理查询变量(Cosmos-SDK 中的 Querier)。

最后，核心契约逻辑位于 cosmwasm/src/contract.rs 中:

这些函数将松散地表示 Cosmos-SDK 中的处理程序。

关于合同需要注意的几点:

*   没有默认的代币面额，因此该合同将接受任何类型的代币
*   为简单起见，回购任何路线都需要相同的令牌，因为我无法访问价格 oracle
*   当从以前的所有者处回购路由时，发送消息包含在 HandleResponse 中，并将在同一个事务中执行

为 CosmWasm 编写一个智能契约比其他方法更加冗长。这部分是由于缺少[程序宏](https://doc.rust-lang.org/reference/procedural-macros.html)的使用。还不清楚与 Cosmos-SDK 架构的相似性是否是基于技术动机，或者是否旨在让 Cosmos 开发人员更熟悉，但如果来自这种背景，就会更容易理解。不幸的是，这并不是非常地道的 Rust 代码，对于人们来说并不容易理解和使用。与此同时，内部类型很少被记录，所以当有些东西不清楚时，可能的做法是通过例子找到用法。

同样值得注意的是，还有 [CosmWasm Plus](https://github.com/CosmWasm/cosmwasm-plus) ，它是为“真实网络”设计的，并且有与基本 CosmWasm 库重叠的库，所以当深入研究时，这样做的动机可能会更清楚。

测试合同是通过原生锈蚀测试，类似于 NEAR 和 ink！文档中提到，如果测试通过，它们将在链上工作。

CosmWasm 是 Cosmos 生态系统中一个奇妙的组件，我希望该项目能够继续改进，以获得更广泛的受众。

# 索拉纳

[Solana](https://solana.com/) 是一个非常有趣的区块链，因为它的协议设计与所有的替代方案完全不同。许多个人观点超出了本文的解释范围，因此我将列出一些有趣的观点:

*   通过 LLVM 基础设施编译的 Rust 或 C/C++智能合约
*   编译成 [BPF](https://en.wikipedia.org/wiki/Berkeley_Packet_Filter) 字节码，在链上加载
*   基于被称为历史证明的同步时钟的共识机制
*   PoS 的 PoH 扩展
*   交易与一个 [VDF](https://blog.trailofbits.com/2018/10/12/introduction-to-verifiable-delay-functions-vdfs) 样本一起打上时间戳
*   用本地计算取代低延迟通信瓶颈
*   能够对交易进行亚秒确认
*   智能合同的并行执行
*   反对基于 Wasm 的智能合约的理由是，它们目前都是单线程的
*   描述哪个状态是读/写的，并且可以基于类似于[读写锁](https://en.wikipedia.org/wiki/Readers%E2%80%93writer_lock)的语义进行并行化
*   SIMD 喜欢根据程序 ID 执行指令
*   优化存储以充分利用操作系统
*   存储在内存映射文件中的帐户
*   如果所有状态都保存在内存中，他们的 Cloudbreak 存储的性能将接近
*   特别是不分片(Vision 在不分片的情况下尽可能快地缩放，然后仅在必要时添加)

如果很多都不熟悉，只要知道这些策略和设计与区块链的老路相去甚远，那就太棒了！这些事情中肯定有权衡，但不可否认的是，索拉纳协议给区块链生态系统带来了大量的创新。

至于智能合约环境，智能合约可以用 Rust 或 C 编写，并编译成 BPF，这是在链上部署的字节码。虽然支持 C，但是围绕 Rust 有更多的工具和资源，所以建议使用它。开发 Solana 程序有两种主要方式:使用 Solana 工具的[普通 Solana 程序](https://docs.solana.com/developing/on-chain-programs/developing-rust)或者使用编写 Solana 程序的框架 [Anchor](https://github.com/project-serum/anchor) 。在本文中，我将使用 Anchor，因为它抽象了许多繁琐和滑稽的逻辑，并且更接近于本文中比较的备选方案。

在开始编写程序之前，首先要做的是帐户模型。它与其他区块链非常不同。Solana 的帐户类似于 Linux 操作系统中的文件，因为它们可以保存任意数据，还包含关于如何访问它们的元数据。与操作系统中的文件的区别如下:

*   这些文件有一个生命周期，基于多少灯(或令牌)已经支付租金
*   这些帐户由 256 位公钥索引
*   这些帐户有固定的大小，不能调整大小。是的，你没看错，我稍后会谈到更多的含义。

先说写程序吧。程序只是被标记为可执行的帐户。Solana 依赖帐户进行存储，因为它在并行处理事务时不能有底层存储。这意味着，在部署程序之前，如果要升级程序，您需要估计帐户需要有多大(或者保留默认值，分配比当前需要多 2 倍的数据)，而且应用程序中使用的任何状态也需要根据估计进行预分配和初始化。目前，短期内还没有允许可调整帐户的计划，所以这是需要记住的重要一点。另一个重要的限制是程序没有返回值，因此如果您需要从程序调用中检索结果，需要将结果写入帐户并进行离线解释。

还需要注意的是，令牌只是一个[程序](https://spl.solana.com/token)，其中数据在一个帐户中表示，每个令牌帐户都有一个所有者。代币面额仅由公钥表示。

当执行交易时，交易中访问的所有账户都需要由用户预先指定。这对任何应用程序开发人员来说都是一个障碍，因为他们需要知道事务中使用的所有账户，当考虑到应用程序之间的[跨程序调用](https://docs.solana.com/developing/programming-model/calling-between-programs)时，这变得更加困难。这使得可用性和可组合性更加困难，但也要记住，在计算、调用深度、调用深度、事务大小限制和堆栈帧大小等方面也有更严格的限制，这通常会迫使事务和/或函数被拆分。

现在谈谈[合同](https://github.com/austinabell/smart-contracts-compare/blob/main/anchor/programs/plutocratic-hosting/src/lib.rs)。不幸的是，考虑到 Solana 中编程模型的限制，如果不将应用程序逻辑移出链外或修改功能以降低效率并受固定帐户大小的限制，我们实际上无法构建与其他选项相同的功能。这个特定的契约逻辑需要两件事情中的一件:某种可以序列化为字节的键到值的映射，或者能够将值存储在持久化的键-值存储中，其中值是单独存储的。后者更受欢迎，并在上面的备选方案中使用，因为它意味着只加载所使用的内容，并避免了大量的序列化逻辑，但让我来解释一下为什么这两种方法都不可能:

地图:

*   HashMap 所需的 std::hash lib 的组件在 Solana 中不受支持(T7 ),其他第三方哈希算法也是如此(我甚至尝试在 Anchor 中专门添加对 Map 的支持)
*   由于帐户大小固定，因此用作存储的帐户需要预先分配不合理的字节数来进行扩展
*   序列化和映射功能的计算量将很快达到上限
*   拥有一个可增长的向量(String，ContentData)可以编译，但是会遇到所有其他的点

键值存储:

*   不能访问存储，只能访问帐户
*   将一个[程序地址](https://docs.solana.com/developing/programming-model/calling-between-programs#using-program-addresses)映射到一个有数据的账户需要离线应用程序逻辑，对于我们的应用程序来说，没有一个规范“路线”的真实来源

由于这些限制，并且因为我在这方面花了很多时间，我将遍历的合同将只允许初始化一个帐户(表示一条路线),并且程序将拥有一个令牌库，该令牌库将被锁定在程序中，直到有人购买更多相同面值的令牌。该路由也没有 getter 函数，因为 Solana 程序调用没有返回值，这意味着该逻辑将全部发生在链外。

主播入门可以在[这里](https://project-serum.github.io/anchor/getting-started/introduction.html)找到。

这是合同代码:

为了在较高层次上解释正在发生的事情，所有将被访问的帐户(类似于存储)都在一个结构中用#[derive(Accounts)]对每个调用进行了注释。此合约的函数是 Initialize 和 purchase，Initialize 为初始所有者初始化帐户数据，Purchase 允许任何人购买更多令牌。可以传递给函数的其他数据是函数参数，这些数据只是保存在内存中，而不是持久化。您可以在 initialize 和 purchase 函数中看到这些参数，以及保存交易所需帐户的上下文。

接下来，契约的状态位于用#[account]注释的 ContentRecord 结构中，以指示它表示帐户的数据布局。

当从程序中转移令牌时，转移调用由程序签名，这个签名大致等于一个带有私钥的签名，只是只有程序可以生成它。

提取资金的功能在这里不适用，因为没有合同所有者。这意味着如果没有提款功能，合同的范围就更小了，而且有资金被锁在程序的金库里，任何人都无法获得。

测试契约是通过 mocha 测试框架完成的，运行 anchor test，并通过 solana 安装中包含的 solana-test-validator 处理契约的部署和交互。这些测试很棒，因为它是全 e2e 测试，大致相当于前端如何与程序交互。

通过查看契约的[测试，可以更好地了解帐户如何初始化以及如何附加到函数调用。需要注意的一点是，交易中包含的指令在交易之前运行，这对于创建交易中使用的账户至关重要。](https://github.com/austinabell/smart-contracts-compare/blob/main/anchor/tests/plutocratic-hosting.spec.ts)

总之，在目前的状态下，索拉纳并不是一个聪明的承包环境，很容易跳进去，建立一些东西。Solana 可以提供很好的可伸缩性，但是只有当应用程序符合编程模型时，您才必须选择 Solana。我很难推荐某人从 Solana 开始，因为由于当前的协议设计，开发人员的体验和限制比备选方案差得多。

# 结论

就是这样！如果你已经做到这一步，感谢你的阅读！如果有任何问题或者其他你想在评论中提及的事情，请告诉我。您是否想了解更多关于其他智能合约语言/环境的信息、获得代码链的部署过程和/或从前端到这些合约的交互，以便更好地理解整个堆栈？

> 加入 Coinmonks [电报集团](https://t.me/joinchat/JaY3hVVw2WhiNmFl)，了解加密交易和投资

## 另外，阅读

*   最好的[加密交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a) | [网格交易机器人](https://blog.coincodecap.com/grid-trading)
*   [加密复制交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [如何在 WazirX 上购买比特币](/coinmonks/buy-bitcoin-on-wazirx-2d12b7989af1)
*   [CoinLoan 审核](/coinmonks/coinloan-review-18128b9badc4)|[Crypto.com 审核](/coinmonks/crypto-com-review-f143dca1f74c) | [火币保证金交易](/coinmonks/huobi-margin-trading-b3b06cdc1519)
*   [尤霍德勒 vs 考尼洛 vs 霍德诺特](/coinmonks/youhodler-vs-coinloan-vs-hodlnaut-b1050acde55a) | [Cryptohopper vs 哈斯博特](https://blog.coincodecap.com/cryptohopper-vs-haasbot)
*   [杠杆代币](/coinmonks/leveraged-token-3f5257808b22) | [最佳密码交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [Paxful 点评](/coinmonks/paxful-review-4daf2354ab70)
*   [加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南| [如何做空比特币](/coinmonks/how-to-short-bitcoin-568a2d0b4ae5) | [1xBit 回顾](https://blog.coincodecap.com/1xbit-review)
*   [如何在印度购买比特币？](/coinmonks/buy-bitcoin-in-india-feb50ddfef94) | [WazirX 评论](/coinmonks/wazirx-review-5c811b074f5b) | [BitMEX 评论](https://blog.coincodecap.com/bitmex-review)
*   [印度比特币交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9) | [比特币储蓄账户](/coinmonks/bitcoin-savings-account-e65b13f92451)
*   [币安收费](/coinmonks/binance-fees-8588ec17965) | [Botcrypto 审查](/coinmonks/botcrypto-review-2021-build-your-own-trading-bot-coincodecap-6b8332d736c7) | [Hotbit 审查](/coinmonks/hotbit-review-cd5bec41dafb) | [KuCoin 审查](https://blog.coincodecap.com/kucoin-review)
*   [我的密码交易经验](/coinmonks/my-experience-with-crypto-copy-trading-d6feb2ce3ac5) | [购买硬币评论](https://blog.coincodecap.com/buycoins-review)
*   [逐位融资融券交易](/coinmonks/bybit-margin-trading-e5071676244e) | [币安融资融券交易](/coinmonks/binance-margin-trading-c9eb5e9d2116) | [超位审核](/coinmonks/overbit-review-9446ed4f2188)
*   [加密货币储蓄账户](/coinmonks/cryptocurrency-savings-accounts-be3bc0feffbf) | [YoBit 审核](/coinmonks/yobit-review-175464162c62) | [Bitbns 审核](/coinmonks/bitbns-review-38256a07e161)
*   [Botsfolio vs nap bots vs Mudrex](/coinmonks/botsfolio-vs-napbots-vs-mudrex-c81344970c02)|[gate . io 交流回顾](/coinmonks/gate-io-exchange-review-61bf87b7078f)
*   [最佳比特币保证金交易](/coinmonks/bitcoin-margin-trading-exchange-bcbfcbf7b8e3) | [萝莉点评](/coinmonks/lolli-review-e6ddc7895ad8) | [比特币保证金交易](https://blog.coincodecap.com/bityard-margin-trading)
*   [创造并出售你的第一个 NFT](https://blog.coincodecap.com/create-nft) | [本地比特币评论](/coinmonks/localbitcoins-review-6cc001c6ed56)
*   [加密保证金交易交易所](/coinmonks/crypto-margin-trading-exchanges-428b1f7ad108) | [赚取比特币](/coinmonks/earn-bitcoin-6e8bd3c592d9) | [Mudrex 投资](https://blog.coincodecap.com/mudrex-invest-review-the-best-way-to-invest-in-crypto)
*   [如何在印度购买以太坊？](https://blog.coincodecap.com/buy-ethereum-in-india) | [如何在币安购买比特币](https://blog.coincodecap.com/buy-bitcoin-binance)
*   [顶级付费加密货币和区块链课程](https://blog.coincodecap.com/blockchain-courses) | [币安评论](/coinmonks/binance-review-ee10d3bf3b6e)
*   [MXC 交易所评论](/coinmonks/mxc-exchange-review-3af0ec1cba8c) | [Pionex vs 币安](https://blog.coincodecap.com/pionex-vs-binance) | [Pionex 套利机器人](https://blog.coincodecap.com/pionex-arbitrage-bot)
*   [在美国如何使用 BitMEX？](https://blog.coincodecap.com/use-bitmex-in-usa) | [BitMEX 评论](https://blog.coincodecap.com/bitmex-review)