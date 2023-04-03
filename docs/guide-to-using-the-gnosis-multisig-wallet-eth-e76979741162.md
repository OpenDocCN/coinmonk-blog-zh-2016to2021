# Gnosis Multisig Wallet- ETH 使用指南

> 原文：<https://medium.com/coinmonks/guide-to-using-the-gnosis-multisig-wallet-eth-e76979741162?source=collection_archive---------0----------------------->

本文将介绍如何部署您自己的多重签名钱包，使用 Gnosis Multisig 钱包并使其充当轻型钱包。

在本指南中，我将使用:

*   Ropsten Testnet 网络
*   位于[https://wallet.gnosis.pm/](https://wallet.gnosis.pm/)的 Gnosis Multisig UI，为 Ropsten 网络选择设置
*   Ropsten Testnet 乙醚
*   元掩码

如果您没有安装，请安装元掩码，并通过以下链接获取 Ether

[元掩膜安装](/singapore-blockchain-dapps/metamask-metamask-installation-a62865a69d35)

[从 ROPSTEN ethernum test network](/singapore-blockchain-dapps/how-to-get-testcoin-from-ethereum-ropsten-testnetwork-1e443d7524b0)获取乙醚

> 另请阅读:[最佳比特币硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)

# 已连接到元掩码

1.  导航至[https://wallet.gnosis.pm/#/wallets](https://wallet.gnosis.pm/#/wallets)
2.  它将在打开时连接到元掩码.
3.  检查它是否在 Ropsten 测试网络中。如果不在 Ropsten 测试网络中，则在元掩码上更改为 **Ropsten 测试网络**

![](img/7eb56e5b606c800faa518105c3a85eab.png)![](img/a55a9e12802fa4da83a0fb41a53d36c4.png)

4.如果您无法更改为 Ropsten 测试网络，请单击“设置”选项卡。在 Web3 提供下选择**默认(元掩码、雾、奇偶校验……)**，然后点击**更新设置**。您可能需要刷新您的 web 浏览器页面，以使设置变为活动状态。

5.选择 Web3 提供商下的远程节点，选择以太网节点下的**远程服务器**并点击**更新设置**。您可能需要刷新您的 web 浏览器页面，以使设置变为活动状态。

![](img/d4d83676d9f61447ca8836b1c6a1a693.png)

# 步骤:

1.  创建一个钱包并确认谁有多重签名钱包
2.  将乙醚转入多符号钱包
3.  从多签名钱包转移乙醚，但交易会将暂停，等待帐户 2 签名
4.  确认交易中账户 2 的签名
5.  确认账户 2 的签名，然后转账

![](img/df90cf171547c98a3bc969dd26a71091.png)

# 1.部署新的多重签名钱包

1.1 点击**钱包**右侧的**添加**

![](img/b5f0f64b3dec4aa951ccc92962e13fc8.png)

**1.2 点击【新建钱包】**和**下一步**

![](img/61d23f75f2d3d8f4361d2f8946a25f20.png)

1.3 输入 N **ame** 、**必确认、**、**每日限额(ETH)** 及业主地址后**“随厂部署”**:

*   N **名称:钱包名称**
*   **所需确认:**需要多签名钱包的所有者(签名者)批准的次数(不要与交易确认混淆)。这里我输入 2，意思是至少需要两个人签字才能交易。
*   **每日限额(ETH) :** 在**所需确认**中配置的无需所需确认即可撤销的限额。
*   **添加**:这些是作为钱包变更签名人的所有者地址，特别是取款、确认变更、每日限额和所有权变更。

> **Gnosis Multisig 钱包上的姓名**
> 
> 名称(即钱包名称、所有者名称)存储在本地，而不是 Gnosis 多签名智能合约的链上，因此在计算机重新格式化后重新安装和恢复多签名钱包地址时，名称不在那里也不要感到惊讶。在链上，智能合同仅期望所有者地址、所需的确认/批准和每日限额数字。

![](img/cdd3020e4f6d0f7e221d6ee6881fd83d.png)

1.4 点击“**发送交易**”部署 MultiSig 钱包，但需要乙醚支付汽油费。您应该会在右上角看到“部署事务已发送”的消息。

![](img/8b122c383d3d23f52c045b061fa80b15.png)

1.5 点击 MetaMask 弹出窗口中的**确认**。

![](img/79de55a28c2c25de49c14609923da32a.png)

那没花多长时间。一旦交易被确认，我们可以看到钱包显示在钱包标签。如果钱包没有出现，请检查您的交易是否成功。

![](img/8bb26674082785888d874ef1b8c7c4b1.png)

# 2.将乙醚转移到多重签名钱包中

2.1 点击“存款”

![](img/f585f5813a6f54fad441d77ece0b6d77.png)

2.2 输入金额，然后点击“发送交易”

![](img/e2316e9e100955a524ef646e36ed0499.png)

2.3 定制**气体限制**和**气体价格(GWei)** ，点击**发送交易。**

![](img/44ece6d6e93c41fec5242d81e1f5c82f.png)

2.4 在 MetaMask 弹出窗口中点击**确认**。

![](img/32ae5d4540c04511f757889eb080b576.png)

2.4 该数额列于余额项下。

![](img/90e1c8e2b015afd64820abf2ebad59a8.png)

# 3.从多重签名钱包转移以太网

我把每天转账的最大额度设为 1 ETH。一旦超过，它只需要两个或更多的签名。

现在我们从多签钱包转 1.9 ETH。

3.1 按下**撤回**，然后输入 1。9 低于数量(ETH)

3.2 点击**发送多笔交易**。(我们不能把钱包里所有的乙醚都取出来，因为还剩下一点儿乙醚用于加油)

![](img/ec6c2c00d51847d48d6a4610a6f7bd4b.png)

3.3 点击**发送交易**

![](img/ebe980af540aca3f03fadadad1e5394a.png)

3.4 在 MetaMask 弹出窗口中点击**确认**。

![](img/c491acd853b40e36f4d4b7be9cdda16d.png)

# 4.第二个所有者签名交易

4.1 在 MetaMask 中更改到**账户 2(钱包的所有者)。**

![](img/dd12c6b3508240387f2774d6a7e61e1d.png)

4.2 点击**钱包名称**

![](img/76020e5b193178b2ae6bc97356b9ddb6.png)

4.3 点击**确认**下的**确认**

![](img/f9521d7b21edcc7efac9c52089e7de85.png)

页（page 的缩写）如果您看到“**撤销确认**”，则的账户没有改变。"**撤销确认**"仅由调用交易的所有者显示

![](img/a1f4fc9db8b8b22b01592e7f18b6b114.png)

Revoke confirmation

4.4 点击“发送交易”

![](img/0175e4603282fa6604dfb778b99fa478.png)

4.5 点击“发送交易”

![](img/04440533e827549c616fc2b3151541e1.png)

4.5 在 MetaMask 弹出窗口中点击**确认**。

![](img/af7322de0f005109632d26e746f0f398.png)

4.6 交易确认后，所有签署交易的用户都将列在确认列表下。

![](img/a8a2a5a614ce25c50ec40acfd62c58fe.png)

您可以随时在 [https://etherscan.io](https://etherscan.io) 上(或者在这种情况下，在 [ropsten.etherscan.io](http://ropsten.etherscan.io) 上)检查您的交易地址。

账户 1 的交易:

 [## Ropsten 事务 0x 8149 E1 b 2930 E3 a591 c 487 f 331 a 128 f 671052 EC 7 ACB 8 e 26 c 82d 4 f1 BD 307 fc 38d 4

### 0 x8149 E1 b 2930 E3 a 591 c 487 f 331 a 128 f 671052 EC 7 ACB 8 e 26 c 82d 4 f1 BD 307 fc 38d 4 的 Ropsten (ETH)详细交易信息

ropsten.etherscan.io](https://ropsten.etherscan.io/tx/0x8149e1b2930e3a591c487f331a128f671052ec7acb8e26c82d4f1bd307fc38d4) ![](img/d93b8f6a7557c3c77ba56ebc900ffea8.png)

账户 2 的交易:

 [## Ropsten 事务 0 xee 256 b5 B4 adad 16666 b 131d 8 a 6 Fe 42 fc 44700 c 9 cade 043 de 2 b 9 d5a 62 e 8 c 55 DD 3

### 0 see 256 b5 B4 adad 16666 b 131d 8 a 6 Fe 42 fc 44700 c 9 cade 043 de 2 b 9 D5 a 62 E8 c 55 DD 3 的 Ropsten (ETH)详细交易信息

ropsten.etherscan.io](https://ropsten.etherscan.io/tx/0xee256b5b4adad16666b131d8a6fe42fc44700c9cade043de2b9d5a62e8c55dd3) ![](img/35bffbe919b29b77a8ccf560d19382ed.png)

MultiSig 钱包的细节:

 [## Ropsten 帐户、地址和合同

### 以太坊区块链浏览器、API 和分析平台

ropsten.etherscan.io](https://ropsten.etherscan.io/address/0xb9b68f5613093028fda664684586b8ce64e1bba6) ![](img/e1d8ca559e45f29bcb9743a730f0b4f3.png)

下一步:[Gnosis Multisig Wallet-er C20 令牌使用指南](/singapore-blockchain-dapps/guide-to-using-the-gnosis-multisig-wallet-erc20-token-3e305694ae31)

我这样做是因为我喜欢它，但如果你想让我给自己买杯咖啡，我不会拒绝:o)谢谢^^

# 捐赠:

XEM:ncwzsuf 4 fpx jy 3 l3y 7657 qnvbiuz 5d 54 F4 tnj 64s

乙醚:0 xf 2d 15 deaf 62 b 8 C4 AFC 0343006579 e8e 662 c 120d 9

比特币:332 uiyafsxyvhqcygdgbknlfsf 25 ccnv 9 I

**做拍手，评论，分享！我也欢迎任何出现的商业机会***

# 连接:

[*LinkedIn*](https://www.linkedin.com/in/kenneth-hu-1b534664/)

[*电报*](http://bit.ly/2GcnE9x)

[*脸书*](https://www.facebook.com/groups/140517386608407/)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)