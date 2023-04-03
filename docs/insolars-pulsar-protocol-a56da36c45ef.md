# Insolar 的脉冲星协议

> 原文：<https://medium.com/coinmonks/insolars-pulsar-protocol-a56da36c45ef?source=collection_archive---------3----------------------->

## Insolar 区块链 4.0 的支柱

![](img/079a7bb7bf39ff7812dcfcaa1cbafaef.png)

[https://unsplash.com/photos/W8KTS-mhFUE](https://unsplash.com/photos/W8KTS-mhFUE)

在本文中，我们将触及 Insolar 的节点交互层，即所谓的 Pulsar 协议，在最近发表的[技术论文](https://insolar.io/uploads/Insolar%20Tech%20Paper.pdf)中有详细描述。这一层的主要目的是促进网络
同步，并提供一个分类帐内的随机性来源。它利用拜占庭容错(BFT)共识模型，类似于 [Hyperledger](https://www.hyperledger.org/projects/fabric) 。随着通用共识的更详细描述在此[可用](https://people.eecs.berkeley.edu/~luca/cs174/byzantine.pdf)，让我们简单回顾一下脉冲星网络的 BFT 共识是如何工作的，并估计该协议的健壮性和可扩展性。

时间轴被分割成固定的时间间隔，称为*脉冲*(或者更传统的视图)。在每个脉冲，节点经历几个阶段:

**阶段 1** :节点准备、签名并向所有节点广播 PulseData 有效载荷。
**阶段 2** :节点从集群内所有其他节点接收带有签名的 PulseData 有效载荷，称为 *Globula* 。节点验证签名并从发送/接收列表中删除恶意节点。有效的脉冲星 id 和特征被广播到所有节点。
**第三阶段:**节点以 2/3 多数对 PulseData 达成共识，然后根据 pulse data 建议集的哈希选择胜出的 Pulsar。获胜的 Pulsar 提供加密密钥来解密其 PulseData 有效载荷。
**阶段 4:** 节点验证胜出的 Pulsar 加密密钥，向节点广播另一个签名以确认验证。之后，它接收类似的签名，并且在成功验证 2/3 多数之后，PulseData 有效载荷被最终确定并保持。

技术论文没有讨论可能的**攻击媒介或瓶颈**，但是有一些:

**34%攻击**:这是任何基于 BFT 的分布式分类账的已知问题。减轻攻击的一个方法是设置选择脉冲星的规则，并对获取脉冲星 ID 应用权限限制。

**无声鲍勃攻击**:在第三阶段，获胜的脉冲星*被期望*提供签名密钥。鉴于某种恶意的全能，人们可以试图干扰共识。这可以通过从节点发送/接收列表中禁止此类不当行为的 Winning Pulsar 并重启阶段 3 来缓解。

现在让我们检查所有的**瓶颈**:在第 1 阶段，节点将 PulseData 有效载荷和签名一起传播到每个其他节点。在第二阶段，节点需要验证来自其他节点的两个签名，然后向节点广播 Pulsar ID。在第四阶段，他们需要再次广播胜出的脉冲星确认信号。每次节点与 *Globula* 集群中的所有其他节点进行通信，集群最多可达 1000 个节点。因为技术论文没有提到任何关于加密方案的细节，所以我假设是 RSA-2048。
让我们快速估算一下:

***签名验证开销*** : RSA-2048 签名验证采用优化的 C++库，运行于英特尔酷睿 2 1.83 GHz 处理器([来源](https://www.cryptopp.com/benchmarks.html))上，耗时约 0.16 毫秒/签名。由于节点每个脉冲需要进行大约 2000 + 2/3*1000 次验证，因此每个脉冲 **0.42 秒**，这是一个可以忽略的小数字，不会导致任何网络延迟或节点不同步。

***带宽要求****:*64B(Pulse data)+128B(NodeStateProof)+256 b(脉冲星签名 S1)+ 256B(脉冲星签名 S2) + 64B(脉冲星 ID) + 256B(获胜脉冲星的验证签名 S3) +假设 1024B(有效载荷字节)= **每个节点每个脉冲 2kB**，这告诉我们任何一个带宽合理的节点都可以参与脉冲星的一致。

总之，可以清楚地观察到，在当前设置中，Pulsar 协议并不妨碍 Insolar 业务网络平台上层的扩展。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)