# 发布后 EOS mainnet 上的安全命令行投票指南

> 原文：<https://medium.com/coinmonks/guide-to-secure-command-line-voting-on-the-eos-mainnet-ddcc3fbe0db?source=collection_archive---------7----------------------->

![](img/c601ef7165dd9e496fdc1fb95d4de544.png)

[source](https://steemkr.com/eos/@trillex/eosio-fan-challenge-3)

我们将在 EOS mainnet 上一步一步地为你最喜欢的制作人候选人投票。

> ** *请注意，本教程仅适用于 EOS mainnet 上的投票。mainnet 启动过程将于 6 月 3 日开始。请定期查看以下状态，以了解主网何时上线并准备好投票。一旦主网准备好投票，我们将更新此状态****

## **状态:EOS 主网准备投票**

— — — — — — — — — — — — — — — — — — — — — — — — — —

本教程使用我们在 [EOSCanada](https://www.eoscanada.com/) 的朋友创建的 eosc 命令行工具。这是一个简单的命令，将 EOS 命令行钱包和投票所需的工具打包到一个易于使用的包中。

**第一步:** **根据您的操作系统版本下载合适的 eosc 可执行文件，**
[*https://github.com/eoscanada/eosc/releases*](https://github.com/eoscanada/eosc/releases)

或者您可以使用这里的说明从源代码构建它，

[*https://github.com/eoscanada/eosc*](https://github.com/eoscanada/eosc)

**第二步:接下来，您可以通过“eos 保险库导入”操作导入 EOS 私钥，为投票做好准备，**

```
$ eosc vault import
Vault file not found, creating a new wallet
Type your first private key:        [insert your private key here]
Type your next private key or hit ENTER if you are done:
Keys imported. Let's secure them before showing the public keys.
Enter passphrase to encrypt your keys:
Confirm passphrase:
Wallet file "./eosc-vault.json" written. Here are your public keys:
- EOS59seMRezsBjaWsdQBDbyqKbTJL25CGqfURHrBP1YREEuNQbaq5
```

保险存储操作不需要网络调用，并且可以脱机完成。`./eosc-vault.json` 文件经过加密，可以安全存档，是未来的证据。您可以在这里检查用于加密的密码[。](https://github.com/eoscanada/eosc#cryptography-used)

**步骤 3:一旦你使用步骤 2 将私钥导入到本地保险库中，你就可以投票了。**

```
$ eosc vote producers youraccount blockgenicbp producercnd1 producercnd2  -u http://api.blockgenicbp.com
Enter passphrase to unlock vault:
Voter [youraccount] voting for: [blockgenicbp]
Done
```

这个投票事务将在本地签名，并通过`[http://api.blockgenicbp.com](http://api.blockgenicbp.com)`端点提交给网络。您可以将-u 标志指向主网络上的任何有效端点，它应该可以工作。请注意，只有在 mainnet 启动并准备好接收投票后，这个投票命令才会起作用。因此，请检查这一页顶部的状态，以确保主网准备好接收投票。否则，您的投票将无法注册，一旦网络稳定下来，您将不得不重新投票。