# Gnosis Multisig Wallet-ERC20 令牌使用指南

> 原文：<https://medium.com/coinmonks/guide-to-using-the-gnosis-multisig-wallet-erc20-token-2122f8024265?source=collection_archive---------1----------------------->

本文将介绍如何使用 Gnosis Multisig Wallet 部署自己的多签名钱包，并让它充当一个轻便的钱包。

## 在本指南中，我将使用:

*   Ropsten Testnet 网络
*   位于[https://wallet.gnosis.pm/](https://wallet.gnosis.pm/)的 Gnosis Multisig 用户界面，带有为 Ropsten 网络选择的设置
*   Ropsten Testnet 乙醚
*   元掩码
*   肯尼斯·托肯.地址:[0 XB 6d 15 ad 59 dead 5c 74 cf 62 a6 c 0 e 57774 b 92 a 488](http://0xb6d1D15Ad59DeaD5C74Cf62a6C0e57774b92A488)请在回复中留下你的钱包地址，如果你没有代币，我会给你发代币。

如果你没有安装，请安装 MetaMask，并通过以下链接获取以太网

[MetaMask 安装](/singapore-blockchain-dapps/metamask-metamask-installation-a62865a69d35)

[从以太网 ROPSTEN testnetwork 获取以太网](/singapore-blockchain-dapps/how-to-get-testcoin-from-ethereum-ropsten-testnetwork-1e443d7524b0)

> 另请阅读:[最佳硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)

# 连接到元掩码

1.  导航到[https://wallet.gnosis.pm/#/wallets](https://wallet.gnosis.pm/#/wallets)
2.  当打开 gonsis 时，它将连接到元掩码.
3.  检查它是否在 Ropsten 测试网络中。如果不在 Ropsten 测试网络中，在元掩码上切换到 **Ropsten 测试网络**

![](img/7eb56e5b606c800faa518105c3a85eab.png)![](img/a55a9e12802fa4da83a0fb41a53d36c4.png)

4.如果您无法更改到 Ropsten 测试网络，请单击“设置”选项卡。在 Web3 Provide 下选择“ **Default (MetaMask，Mist，Parity …)** ”，然后点击“ **Update settings** ”。您可能需要刷新 web 浏览器页面，以使设置生效。

5.选择 Web3 provider 下的“远程节点”，选择以太坊节点下的“**远程 Ropsten”**，点击“**更新设置**”。您可能需要刷新 web 浏览器页面，以使设置生效。

![](img/d4d83676d9f61447ca8836b1c6a1a693.png)

# 步骤:

1.  创建一个钱包并确认谁有多重签名钱包
2.  将乙醚转移到多重签名钱包中
3.  将令牌添加到令牌列表中
4.  将令牌转移到多签名钱包中
5.  从多重签名钱包中转移令牌，但交易会将暂停，等待帐户 2 签名
6.  确认账户 2 在交易中的签名
7.  确认账户 2 的签名，然后转移令牌

![](img/ac98d40925282be5382295a8307c3237.png)

# 1.部署新的多重签名钱包

1.1 点击**钱包**右侧的**添加**

![](img/b5f0f64b3dec4aa951ccc92962e13fc8.png)

**1.2 点击【创建新钱包】**和**下一步**

![](img/61d23f75f2d3d8f4361d2f8946a25f20.png)

1.3 输入 N **ame** 、**需确认、**、**每日限额(ETH)** 及业主地址，然后**“工厂调配”**:

*   名称:钱包名称
*   **所需确认:**需要多签名钱包的所有者(签名者)批准的次数(不要与交易确认混淆)。这里我输入 2，意思是至少需要两个人签字才能交易。
*   **每日限额(ETH) :** 在**要求确认**中配置的无需要求确认即可撤销的限额。
*   **添加**:这些是作为钱包变更签名人的所有者地址，特别是取款、确认变更、每日限额和所有权变更。

> **Gnosis Multisig 钱包上的姓名**
> 
> 名称(即钱包名称、所有者名称)存储在本地，而不是 Gnosis 多签名智能合约的链上，因此在计算机重新格式化后重新安装和恢复多签名钱包地址时，名称不在那里也不要感到惊讶。在链上，智能合同仅期望所有者地址、所需的确认/批准和每日限额数字。

![](img/cdd3020e4f6d0f7e221d6ee6881fd83d.png)

1.4 点击“**发送交易**部署 MultiSig 钱包，但需要乙醚支付汽油费。您应该会在右上角看到“部署事务已发送”的消息。

![](img/8b122c383d3d23f52c045b061fa80b15.png)

1.5 在 MetaMask 弹出窗口中点击**确认**。

![](img/79de55a28c2c25de49c14609923da32a.png)

那没花多长时间。一旦交易被确认，我们可以看到钱包显示在钱包标签。如果钱包没有出现，请检查您的交易是否成功。

![](img/8bb26674082785888d874ef1b8c7c4b1.png)

# 2.将乙醚转移到多重签名钱包中

2.1 点击“存款”

![](img/f585f5813a6f54fad441d77ece0b6d77.png)

2.2 输入金额，然后点击“发送交易”

![](img/e2316e9e100955a524ef646e36ed0499.png)

2.3 自定义**气限**和**气价(GWei)** ，点击**发送交易**:

![](img/44ece6d6e93c41fec5242d81e1f5c82f.png)

2.4 在 MetaMask 弹出窗口中点击**确认**。

![](img/32ae5d4540c04511f757889eb080b576.png)

2.4 该数额列于余额项下。

![](img/90e1c8e2b015afd64820abf2ebad59a8.png)

# 3.将令牌添加到令牌列表中

3.1 点击**钱包名称**

![](img/9d9a2995dec97de5121ac2754d5c64d6.png)

3.2 点击令牌上的“添加”

3.3 输入令牌地址，然后将显示名称、符号和小数。

![](img/54f3f636818c86395f284eb26027997d.png)

# 4.将令牌转移到多签名钱包中

4.1 点击**存款**

![](img/a9b11b98376b8103b057f449e9f65e59.png)

4.2 输入金额后点击**发送交易**

![](img/10f763ce07093263d5d9f4c8ce5e8b99.png)

4.3 自定义**气限**和**气价(GWei)** ，点击**发送交易。**

![](img/303e31e72d0c672b39ac4c28eef26813.png)

4.4 点击 MetaMask 弹出窗口中的**确认**。

![](img/b67ef50d108f25730f17fe55f844066c.png)

4.5 10 代币将显示在多联余额下

![](img/55565cb075f6fac40c40712eaf6ead31.png)

# 5.从多重签名钱包转移令牌

5.1 在 MetaMask 中更改到**帐户 2(钱包的所有者)。**

![](img/45db465c340d5fea3df023edae4de5d7.png)

5.2 按下**撤回**，然后在金额(ETH)下输入 10

5.3 点击**发送多笔交易**。

![](img/45a3e0d5b6f40b9b6ae016a58e40a43a.png)

5.4 定制**气体限值**和**气体价格(GWei)** ，点击**发送交易。**

![](img/b707cb6055cfe89e5bd901ed6e60ef67.png)

5.5 点击 MetaMask 弹出窗口中的**确认**。

![](img/5599d6ef5413dd3fd163c663dd0325ec.png)

交易确认后，您可以刷新页面。但是，令牌仍将保留在钱包中。这是因为它需要第二个所有者的签名。

![](img/db960448709c58c9c1ba3881be42d112.png)

# 6.第二个所有者签名交易

6.1 在 MetaMask 中改回**账户 1(钱包的主人)。**

![](img/6ffc0259285e2726140fed0881f91599.png)

6.2 点击**确认**下的**确认**，然后点击**发送交易**

页（page 的缩写）如果您看到“**撤销确认**”，则表示账户未变更。“**撤销确认**”仅由调用交易的所有者显示

![](img/066ef4126781720e1d2093039b672ad0.png)

6.3 定制**气限**和**气价(GWei)** ，点击**发送交易。**

![](img/67be3c45d8b71e39752386b4698d6703.png)

6.4 点击 MetaMask 弹出窗口中的**确认**。

![](img/57426334ef6caa9d5680e37ebe606eab.png)

交易确认后，所有签署交易的用户都将列在确认列表下。

![](img/542e76a89b34e9e048365d3e02515f0d.png)

您可以随时在 [https://etherscan.io](https://etherscan.io) 上查看您的交易地址(或者在这种情况下，在 [ropsten.etherscan.io](http://ropsten.etherscan.io) 上查看)。

账户 2 的交易:

 [## Ropsten 事务 0x 0 B4 cc 60545 CAC B1 DCC fc 98 adafb 607 c 0072d 467 e 47 de 86 ceea 3d 87 FCE 9715237

### 0x 0 B4 cc 60545 CAC B1 dccf c 98 adafb 607 c 0072d 467 e 47 de 86 ceea 3d 87 FCE 9715237 的 Ropsten (ETH)详细交易信息

ropsten.etherscan.io](https://ropsten.etherscan.io/tx/0x0b4cc60545cacb1dccfc98adafb607c0072d467e47de86ceea3d87fce9715237) ![](img/3224fc185c16f5e8da52fa4a99fe402d.png)

账户 1 的交易:

 [## Ropsten 事务 0x 53 e 7 e 93d 9 FD 0e 6771 a 01 b 85d 24 FB 557400 b 47465315467 a0b 582314806 febf 97

### 0 x 53 e 7 e 93d 9 FD 0e 6771 a 01 b 85d 24 FB 557400 b 47465315467 a0b 582314806 febf 97 的 Ropsten (ETH)详细交易信息

ropsten.etherscan.io](https://ropsten.etherscan.io/tx/0x53e7e93d9fd0e6771a01b85d24fb557400b47465315467a0b582314806febf97) ![](img/8ee7fe6b686d77f8182100d47b684af8.png)

MultiSig 钱包的细节:

 [## Ropsten 帐户、地址和合同

### 以太坊区块链浏览器、API 和分析平台

ropsten.etherscan.io](https://ropsten.etherscan.io/address/0xb9b68f5613093028fda664684586b8ce64e1bba6) ![](img/e1d8ca559e45f29bcb9743a730f0b4f3.png)

下一步:[Gnosis Multisig Wallet-ETH 使用指南](/singapore-blockchain-dapps/guide-to-using-the-gnosis-multisig-wallet-eth-16b4277dff55)

我这样做是因为我喜欢它，但如果你想让我给自己买杯咖啡，我不会拒绝:o)谢谢^^

# 捐赠:

XEM:ncwzsuf 4 fpx jy 3 l3y 7657 qnvbiuz 5d 54 F4 tnj 64s

乙醚:0 xf 2d 15 deaf 62 b 8 C4 AFC 0343006579 e8e 662 c 120d 9

比特币:332 uiyafsxyvhqcygdgbknlfsf 25 ccnv 9 I

**做拍手，评论，分享！我也欢迎任何出现的商业机会***

# 连接:

[*领英*](https://www.linkedin.com/in/kenneth-hu-1b534664/)

[*电报*](http://bit.ly/2GcnE9x)

[*脸书*](https://www.facebook.com/groups/140517386608407/)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)