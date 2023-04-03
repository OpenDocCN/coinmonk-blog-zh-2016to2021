# 这就是我们如何在 1.0 #EOSIO 发布前 11 天连续两次攻破 Jungle Testnet 的

> 原文：<https://medium.com/coinmonks/thats-how-we-broke-jungle-testnet-twice-in-a-row-eleven-days-before-1-0-eosio-release-ef996495df?source=collection_archive---------4----------------------->

**首先**:本文与 2018 年 6 月 16 日 EOS Mainnet 暂停没有任何关系。本文涵盖了 2018 年 5 月 20 日 Jungle Testnet 中的受控压力测试。

![](img/1e1b4def3e3033665b255621664c9381.png)

2018 年 5 月 20 日—黎明 4.0 EOS 版(1.0 版发布前 11 天)

[EOSMetal](https://medium.com/u/af7c15cbbf20?source=post_page-----ef996495df--------------------------------) 团队，EOS BP 候选人，积极参与 Jungle Testnet(EOS Mainnet 启动前最重要的测试网)和我们的节点“mosquito meta”(64GB 内存和 8 个内核)。我们的目的是及时了解与该软件相关的所有新闻，当然我们想将网络推向极限，以了解它能承受多远。

我们知道，凭借我们在关键系统领域 20 多年的经验，我们可以为社区做些好事。

Jungle Testnet 是一个试验和学习 EOSIO 软件的好地方。这个测试网是由 Cryptolions 管理的，主要是 Bohdan 作为系统管理员，在他们的 Github 中有一个非常活跃的电报通道和许多非常好的 EOSIO 软件。

我们的第一个目标是了解 Testnet 节点每秒可以处理多少事务，并使用 Testnet Monitor(Cryptolions 的另一个伟大工具)实时监控所有节点的状态。为此，我们使用了一个简单的 Linux 脚本，它使用每个节点的 HTTP 端点向网络发送随机事务。为了开始压力测试，我们首先需要使用丛林测试网资源的标记，所以我们向 Bohdan 请求:

![](img/99e06bc47c1abad28e2e451e9ee16e4c.png)![](img/58862e2e8bd762a43df42117d6c60bbe.png)

Bohdan transferred 10Millions to our account

![](img/53ac1760f0723e63dacc2a2c3047789e.png)

With the staked tokens, we have plenty of CPU and Net Bandwidth to execute transactions in Jungle Testnet

![](img/9a296b3d7d3e2c337fdddc35a081a0da.png)

Some explanations from [Daniel Larimer](https://medium.com/u/1d906c7421de?source=post_page-----ef996495df--------------------------------) about how limits works

当然啦！丛林测试网的其他 BP 加入了 TRX 党

![](img/b4e4d2ea2d1c7ffb2eaefdfb2ca5dc58.png)

派对开始了！

![](img/c9e0d046bb208c97e64fd8aec041ff94.png)

We used [https://github.com/CryptoLions/TX-test-sender](https://github.com/CryptoLions/TX-test-sender)

开始几分钟后，我们得到了第一个结果(大量的红色)

![](img/cbb1b1152fbc81d43656ab47f5456397.png)

most nodes were flooded, and other Bps wanted join to the party too

![](img/214eb8c6402db49cb6dc698d12bba7ee.png)![](img/b0a09377c3e93c1af6ef51723c5ab9a1.png)

而噩梦，开始了…

![](img/d0533c69e61e7f111f87fe78e11c9776.png)

The effect was nodeos service consuming 100% CPU

![](img/64f32f0b18b31fef5c9e4696e513d1ab.png)![](img/75feab928b952159e2b48affb7dafa29.png)

We informed [Daniel Larimer](https://medium.com/u/1d906c7421de?source=post_page-----ef996495df--------------------------------) about our test results

关于资源限制的一些解释

![](img/218a78a9e5ea7d7c367d9c42da2ff4ea.png)![](img/7ef50dc288f74f0c967252db8809ef4c.png)

在这种情况下，有可能恢复丛林测试网，我们再次开始，但与一个更重的压力测试发布交易与 20k 的长备忘录

![](img/8f317bdcfd3809afd97ba38f86fed826.png)

噩梦又开始了…

![](img/bd4ab91d2afc724f288d6e3696cf7269.png)![](img/f1a6208e858270d038a73c4dd07430f1.png)![](img/3b2fc41fae7d36a0f01d6a3af1fe5f14.png)

几分钟后，所有节点被淹没，最后丛林测试网崩溃

![](img/f8db1464f5ed5f007eba019b42a8634d.png)

Alfter request of Bohdan, We stopped the flood

![](img/2c6f6745aeb1eb5bcd63a739ee4aacd7.png)![](img/a68b598a448a98ebc859e4efc56a9526.png)![](img/aeb7ad3fc28356b7ef4a9196d9ac9b80.png)

丛林测试网死了，没有恢复的可能

![](img/e643bbdfee223db6c09c534619791599.png)

还有[丹尼尔·拉里默](https://medium.com/u/1d906c7421de?source=post_page-----ef996495df--------------------------------)帮助了我们

![](img/d9b104b0aa9e308b647bfee8c27514e8.png)![](img/68a5e84bc6fb7632024eb9100aa129d9.png)

New tools for recovery were created

第二天，丛林测试网死亡 24 小时后，经过多次恢复尝试，Blockone 分析了块日志，并建议重新启动链

![](img/9f1670b3f8308c68cd0916f51d321669.png)

超级 Bohdan 开了一家新连锁店

![](img/72725022520c57ad576efaef7f642671.png)![](img/d6c4da89240c59145299020fe6d5574d.png)

All BPs had to rejoin

当然，在 Jungle Testnet 再次启动后的下一步，我们的节点 mosquitometa 重新投入业务并生产区块，我们希望验证前一天发生的事情不是偶然的，因此我们开始了新的压力测试:)

![](img/c751dee21b29ddeb794d02274827ceb9.png)

First, only my node was overloaded

大约两个小时后，噩梦再次出现…

![](img/e5a0ab6737839927937a7a5569e6a69b.png)![](img/c8676518baf9041cb469cec7e501b885.png)![](img/81a4268101da6173f06d91dd0e0cec76.png)

Send TRX with Long Memo is confirmed as the root case of the problems

![](img/bc52f193fe358a40d56956c538708903.png)

All nodes were unsynced

![](img/dbe5b1013e7c4fa009584c63fc1b3e0f.png)

丹尼尔提出了新的改进

![](img/2882bc782929ba2c54309c92cf413434.png)

Blockone, adds a new P2P protocol called bnet for better performance

![](img/95fe58968d39a79a3aa0b52d2366ace1.png)

并修复了交易中备忘录大小的错误

![](img/27eb2d3aaca0ce960e92ab82505d5646.png)

limit memo from 40k to 256 bytes

几个小时后，丛林测试网从零开始重新启动

![](img/179b5758c949e2c4602c75b2858fa044.png)

我们发现了链条中的另一个问题

![](img/5f4960b1f374efc3cf2113db85d231a8.png)![](img/d46473c4862994734ab1932eb3e5c6ab.png)![](img/ded1eb64e96d860554c33464c3908e31.png)

New tag was made and all nodes were updated fixing different issues (same day!)

我们又开始对新标签进行压力测试，结果非常好:

![](img/adb1c98f836aa37ebf4f01591585be57.png)

662 Transactions per second, without problems

一切都很好，我们学到了一些教训

![](img/ffbb60ba64ade17a060b48915443e4e7.png)

还有一些工作要做

![](img/97abd659924e95850d516cd0228b94f2.png)![](img/9ecff94612370a00e808c6625045137f.png)

感谢所有参与 Jungle Testnet 的人以及他们在那两天里的耐心和辛勤工作(没有所有这些专业人士，我们永远无法测试它)，感谢 B1 在我 20 年的经历中所见过的最快的支持。

在这种没有休息的紧张工作之后，我们很高兴能为这一非常重要的事业做出贡献。

**非常重要的是** **重要的是**那些在 mainnet 推出之前一直在 testnets 中努力工作的**小团队****能够获得足够的票数来生存**。这些团队背后没有庞大的营销机器，但他们在技术领域拥有非常优秀的专业人员。

**我们希望继续为社区做出贡献**，帮助为 EOS 创建一个更好、更安全的 mainnet，但为此我们需要您的投票。有了您的投票，我们可以获得奖励，继续合作，在 Mainnet 中测试真正的 dapps，制定灾难恢复程序，并使 EOS mainnet 变得更大更好。

我们没有营销团队，我们是技术系统管理员，我们每天都在真实的生产环境中不间断地工作。我们可以贡献很多，就像我们在丛林中证明的那样。如果你有问题，你可以顺便访问我们的电报频道:[https://t.me/EOSMetal](https://t.me/EOSMetal)和 EOS 西班牙语频道:[https://t.me/eosEs](https://t.me/eosEs)。

当然，你可以为我们投票！敬我们英国石油公司的候选人“eosmetaliobp”

*真诚地，你的*[*EOS metal*](https://medium.com/u/af7c15cbbf20?source=post_page-----ef996495df--------------------------------)*团队*