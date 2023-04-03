# Hyperledger 结构-更新渠道配置

> 原文：<https://medium.com/coinmonks/hyperledger-fabric-updating-channel-configs-45082a5dc9b2?source=collection_archive---------0----------------------->

![](img/765278433dae0c90cd6d652a81927a48.png)

[Hyperledger Fabric Image](https://www.hyperledger.org/blog/2018/11/28/conducting-data-with-concerto-and-hyperledger-fabric)

## 介绍

这是关于 ***在 Hyperledger Fabric*** 中更新渠道配置的分步指南。这些步骤使用 Hyperledger Fabric[***【构建你的第一个网络】(BYFN)***](https://hyperledger-fabric.readthedocs.io/en/release-1.4/build_network.html) 来演示。我们使用 Hyperledger Fabric***v 1.4***。

> 如果你不知道如何启动一个 BYFN，我也会在本教程中提供步骤，不用担心！
> 
> Hyperledger Fabric 中的**通道**是区块链网络中的私有组。每个区块链网络可以包含多个渠道，其中每个渠道都独立于其他渠道，有自己的分类账，并包含(多个)组织。

每个通道都是独立的，因此每个通道都有独立的配置集，如一些策略、一个块中有多少个事务等。我们将讨论如何更新*通道的配置集。*

## *步骤–概述*

*因为更新频道配置的步骤很容易让人迷路(文件太多了！)，我在这里列出了步骤概述:*

1.  *建立一个 BYFN(我们的区块链网络)*
2.  ****取*** 当前通道配置为***my channel***–output:`***config_block.pb***`，为块文件*
3.  *将`***config_block.pb***`配置转换成可理解的 JSON 文件——输出:`***config.json***`*
4.  *将`***config.json***`复制到`***modified_config.json***`，我们将根据需要更新的内容对其进行修改*
5.  *安装 vim 修改 JSON 文件*
6.  *修改`***modified_config.json***`中的配置*
7.  *将`***modified_config.json***`转换为块文件–输出:`***modified_config.pb***`*
8.  *将`***config.json***`转换为块文件—输出:`***config.pb***`*
9.  *计算`***modified_config.pb***`和`***config.json***`之间的差值–输出:`***diff_config.pb***`*
10.  *将`***diff_config.pb***`配置转换成可理解的 JSON 文件——输出:`***diff_config.json***`*
11.  *在`***diff_config.json***` 中添加 ***信封*** 文本—输出:`***diff_config_envelope.json***`*
12.  *将`***diff_config_envelope.json***` 转换为块文件—输出:`***diff_config_envelope.pb***`*
13.  *签署`***diff_config_envelope.pb***` 作为订单管理员(取决于政策)*
14.  *发送渠道更新交易*

*一些问答:*

> *问:在步骤 2 中，为什么那些配置是通过**取块**得到的？*
> 
> *答:这是因为在 Hyperledger Fabric 中，渠道配置是在区块链分类帐中编码的，它们只是区块链中的信息。*
> 
> *问:在第 9 步中，为什么我们需要计算差异？*
> 
> *答:根据 Hyperledger Fabric 文档，“我们能够放弃 Org1 和 Org2 的大量 MSP 材料和修改政策信息，因为这些数据已经存在于渠道的 genesis 块中。因此，我们只需要两种配置之间的增量”。*
> 
> *问:在步骤 13 中，为什么我们需要以 OrdererOrg admin 的身份签署配置块？*
> 
> *答:要看政策。有关 Hyperledger 结构策略的更多详细信息，您可以查看:*
> 
> *(1)[https://hyperledger-fabric . readthedocs . io/en/release-1.4/policies . html？突出显示=政策](https://hyperledger-fabric.readthedocs.io/en/release-1.4/policies.html?highlight=policy)*
> 
> *(2)[https://medium . com/@ reasdom/hyperledger-fabric-damn-implicitmeta policy-44c C9 cc 5c 472](/@reasdom/hyperledger-fabric-damn-implicitmetapolicy-44cc9cc5c472)*

## *步骤 1-建立 BYFN(我们的区块链网络)*

## ***1.1 安装先决条件***

*在本教程中，我们使用 Hyperledger Fabric v1.4 版。*

*首先，您可以按照官方说明安装先决条件:*

1.  *[安装先决条件](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html#prerequisites)*
2.  *[从 Hyperledger Fabric 安装示例、程序和 docker 映像](https://hyperledger-fabric.readthedocs.io/en/release-1.4/install.html)*

## ***1.2 切换到 BYFN 目录***

*让我们切换到 BYFN 目录(*假设您完成了先决条件部分，您应该已经有了所有需要的文件和目录):*

```
*cd fabric**-**samples**/**first**-**network*
```

## ***1.3 调出网络！***

*有一个脚本可以轻松调出网络，运行一下就可以了。谢了，超级账本。*

```
*./byfn.sh up*
```

> *运行此脚本后，您可能需要等待一会儿…*
> 
> *另外，在运行上面的命令之前，记得启动 Docker。*
> 
> **万一你做错了什么，你可以运行下面的命令来关闭网络，并重新启动(而且要小心):*
> 
> *。/byfn.sh down
> 。/byfn.sh up*

## *1.4 访问 Cli*

*有一个 cli Docker 容器是自动创建的，它是一个命令行接口 ***控制节点*** 。*

*让我们进入命令行界面:*

```
*docker exec -it cli bash*
```

*然后，设置一些程序使用的 ***环境变量*** :*

```
*export CHANNEL_NAME=mychannel
export CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
export CORE_PEER_ADDRESS=peer0.org1.example.com:7051
export CORE_PEER_LOCALMSPID="Org1MSP"
export CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
export ORDERER_CA=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem*
```

> *你可以把它们都复制下来，然后粘贴到你的终端上，按下“回车”键。*
> 
> *有点乱，不好意思。*

## ****第二步—取******my channel***的当前通道配置—输出:***config _ block . Pb***，为块文件*

```
*peer channel fetch config config_block.pb -o orderer.example.com:7050 -c $CHANNEL_NAME --tls --cafile $ORDERER_CA*
```

## *第 3 步—将***config _ block . Pb***配置转换为可理解的 JSON 文件—输出: ***config.json****

```
*configtxlator proto_decode --input config_block.pb --type common.Block | jq .data.data[0].payload.data.config > config.json*
```

## *第四步——复制 ***config.json*** 到***modified _ config . JSON***，我们会根据需要更新的内容对其进行修改*

```
*cp config.json modified_config.json*
```

## *步骤 5 —安装 vim 以修改 JSON 文件*

*vim 是 Linux 中著名的编辑工具。*

```
*sudo apt-get update -y
sudo apt-get install vim -y*
```

## *第 6 步—修改***modified _ config . JSON***中的配置*

```
*vim modified_config.json*
```

*在本教程中，让我们编辑 ***BatchSize*** ，这是关于一个块中有多少个事务(默认为 10 个事务/块)。我们要修改***max _ message _ count***。*

1.  ****按“/”***开始搜索文本*
2.  ****键入“最大消息数”****
3.  *那么你的光标应该位于文本的开头"***max _ message _ count***"*

*你看到上面的文字了吗？*

1.  *现在， ***按“I”***进入 vim 中的插入模式(这样我们就可以开始编辑了)，按下后，你应该看到在终端的左下方有“- Insert -”*
2.  *将光标放在“10”上(上面的第 6 行)*
3.  *将“10”改为“11”:*

*注意第 6 行。我们有相同的短信吗？我们只是将每个块的事务数量从 10 个更改为 11 个。*

> *温馨提示(**可选**到本教程):*
> 
> *一般来说，增加这个数字可能会导致更高的吞吐量(以 TPS，每秒事务数为单位)，但太多可能会导致更大的延迟。我个人用 100(你可能需要调整“absolute_max_bytes”，“preferre_max_bytes”，一些 Kafka 设置如果有的话，还有 block release 超时)。如果你有非常强大的机器，这个数字可能会更大。在我的负载测试中，我可以达到超过 2 XXX TPS(GOLang chain code 使用基本的 getState()和 putState()操作，并使用 LevelDB，v1.4 network)。*
> 
> *记得自己进行负载/性能测试，以验证**生产案例**。更多细节你可以看看:[https://arxiv.org/pdf/1805.11390.pdf](https://arxiv.org/pdf/1805.11390.pdf)*

1.  *然后， ***按【ESC】***退出 vim 的插入模式*
2.  *按“shift +:”，键入“wq！”，然后按“回车”。现在您应该离开 vim，修改被保存。*

## *第 7 步—将***modified _ config . JSON***转换为块文件—输出:***modified _ config . Pb****

```
*configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb*
```

## *步骤 8 —将 ***config.json*** 转换为块文件—输出: ***config.pb****

```
*configtxlator proto_encode --input config.json --type common.Config --output config.pb*
```

## *第 9 步—计算***modified _ config . Pb***和 ***config.pb*** —输出:***diff _ config . Pb****

```
*configtxlator compute_update --channel_id $CHANNEL_NAME --original config.pb --updated modified_config.pb --output diff_config.pb*
```

## *步骤 10 —将***diff _ config . Pb***配置转换为可理解的 JSON 文件—输出:***diff _ config . JSON****

```
*configtxlator proto_decode --input diff_config.pb --type common.ConfigUpdate | jq . > diff_config.json*
```

## *步骤 11 —在***diff _ config . JSON***中添加 ***信封*** 文本—输出:***diff _ config _ envelope . JSON****

```
*echo '{"payload":{"header":{"channel_header":{"channel_id":"mychannel", "type":2}},"data":{"config_update":'$(cat diff_config.json)'}}}' | jq . > diff_config_envelope.json*
```

## *步骤 12 —将***diff _ config _ envelope . JSON***转换为块文件—输出:***diff _ config _ envelope . Pb****

```
*configtxlator proto_encode --input diff_config_envelope.json --type common.Envelope --output diff_config_envelope.pb*
```

## *第 13 步—作为订单管理员签署***diff _ config _ envelope . Pb***(取决于政策)*

*在步骤 1 中，在“访问 cli”下，我们的 Cli 通过环境变量表示 Org1 中的 Peer0。*

*在第 13 步中，我们将切换到 order org admin 来执行签名，因为我们的策略要求 order org admin 签署此更新事务。*

> *有关 Hyperledger 结构策略的详细信息，请参考“步骤-概述”一节末尾提供的两个链接*

```
*export CORE_PEER_ADDRESS=orderer.example.com:7050
export CORE_PEER_LOCALMSPID="OrdererMSP"
export CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/users/Admin\@example.com/msp*
```

> *你可以把它们都复制下来，然后粘贴到你的终端上，按下“回车”键。*
> 
> *有点乱(又)，不好意思那个(又):P :P。*

*现在，我们做手语:*

```
*peer channel signconfigtx -f diff_config_envelope.pb*
```

> *如果你足够细心，应该会观察到 diff_config_envelope.pb 在签名前的文件大小比签名后的要小。*
> 
> *而且签多次也没关系，你自己试试吧。*

## *步骤 14 —发送渠道更新交易*

*是时候更新频道了！*

```
*peer channel update -f diff_config_envelope.pb -c $CHANNEL_NAME -o orderer.example.com:7050 --tls --cafile $ORDERER_CA*
```

> *事实上，这个命令也执行签名。我们仍然在步骤 13 中对其进行签名，因为我想使这些步骤变得清晰，这也是出于教学目的——在某些情况下，当策略需要多个管理员签名时，您可能需要在最终签名者提交渠道更新事务之前多次使用步骤 13 中的命令。*

*好了，我们结束了！*

## *额外步骤 15 —验证渠道更新*

*我们如何知道我们真的更新了配置？*

*一种可能的方法是再次获取配置块，并将其转换为 JSON 文件。最后，我们检查***max _ message _ count***是否为 11。*

```
*peer channel fetch config config_block2.pb -o orderer.example.com:7050 -c $CHANNEL_NAME --tls --cafile $ORDERER_CAconfigtxlator proto_decode --input config_block2.pb --type common.Block | jq .data.data[0].payload.data.config > config2.jsonvim config2.json*
```

*接下来，我不提供进一步的步骤来验证，让我们自己尝试一下作为练习。*

***谢谢！如果你喜欢我的故事，** [**请关注我**](/@reasdom) **的新更新！***

## *参考*

*[](https://hyperledger-fabric.readthedocs.io/en/release-1.4/build_network.html) [## 构建您的第一个网络- hyperledger-fabricdocs 主文档

### 编辑描述

hyperledger-fabric . readthedocs . io](https://hyperledger-fabric.readthedocs.io/en/release-1.4/build_network.html) [](https://hyperledger-fabric.readthedocs.io/en/release-1.4/channel_update_tutorial.html) [## 将组织添加到渠道- hyperledger-fabricdocs 主文档

### 确保您已经下载了安装示例、二进制文件和 Docker…中概述的适当的映像和二进制文件

hyperledger-fabric . readthedocs . io](https://hyperledger-fabric.readthedocs.io/en/release-1.4/channel_update_tutorial.html) [![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)*