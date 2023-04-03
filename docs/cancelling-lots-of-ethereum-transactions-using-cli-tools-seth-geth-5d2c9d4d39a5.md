# 使用 CLI 工具取消大量以太坊交易(seth，geth)

> 原文：<https://medium.com/coinmonks/cancelling-lots-of-ethereum-transactions-using-cli-tools-seth-geth-5d2c9d4d39a5?source=collection_archive---------0----------------------->

![](img/3a04e65fd62a258017096baee6f69a2f.png)

Gas price is too damn high

这是一个动荡的时代，以太币价格比比特币价格更不稳定。你含泪发送短信，但他们仍然在天然气价格竞争中失败，并被困太久。你想取消它们。

如果你使用的是浏览器工具，比如 [Metamask](https://metamask.zendesk.com/hc/en-us/articles/360015489251-How-to-Speed-Up-or-Cancel-a-Pending-Transaction) 或者 [MyCrypto](https://support.mycrypto.com/how-to/sending/checking-or-replacing-a-transaction-after-it-has-been-sent#canceling-a-transaction) ，你可以通过它自己的 UI 来完成；但是，如果您更喜欢以批处理或更可组合的方式来完成，下面是如何在命令行中完成。

您需要在系统中安装两个包:`geth` & `seth`和一个以太坊 JSON-RPC 端点。

端点可以是像 [Infura](https://infura.io/) 这样的云服务，也可以是你自己运行`geth`的全节点。如何设置 Infura 或运行一个完整节点超出了本文的范围，所以接下来我们将假设您已经准备好了上述端点之一。

`geth`如上所述，是最流行的以太坊全节点软件之一。但是我们不一定要运行全节点；我们只是用它来管理钱包，这样你的私人密钥就可以安全地保存在本地。

`seth`是一个 CLI 封装器，用于发送以太坊 JSON-RPC 请求。它在`geth`中搜索你的钱包，并向 Infura 或你自己的`geth`全节点发送 tx。

# 安装 geth 并导入您的钱包

`geth`的官方[维基](https://geth.ethereum.org/docs/install-and-build/installing-geth)有在各种操作系统上安装的详细说明。安装后，您可以通过 CLI 导入您的钱包。

将您的私钥放在一个文本文件中(例如`/path/to/your/private-key`)。请注意，这是临时的，为了安全起见，我们稍后将删除该文件。私钥**也不应该**有`0x`前缀。

接下来，用下面的命令导入它。您将被要求输入密码以保护钱包:

```
$ geth account import /path/to/your/private-keyINFO [07-25|15:37:07.145] Maximum peer count                       ETH=50 LES=0 total=50Your new account is locked with a password. Please give a password. Do not forget this password.Password:Repeat password:
```

现在您可以安全地删除`/path/to/your/private-key`，因为它已经被导入并安全地存储在`geth`中，并且您可能不想继续将您的私钥保存在一个纯文本文件中。

# 安装 seth

在安装`seth`之前，你需要安装它的依赖项`nix`，这可以在一行程序中完成；然而，在撰写本文时，在较新的 Mac OS(如 10.15 Catalina 及更高版本)中安装`nix`需要一些额外的设置，所以:

对于 10.15 之前的非 Mac OS 或更早的 Mac OS:

```
sh <(curl -L [https://nixos.org/nix/install](https://nixos.org/nix/install))
```

对于 Mac OS 10.15 及更高版本:

```
sh <(curl -L [https://nixos.org/nix/install](https://nixos.org/nix/install)) --darwin-use-unencrypted-nix-store-volume
```

一旦`nix`安装完毕，你就可以用另一个单命令行程序安装`seth`:

```
curl [https://dapp.tools/install](https://dapp.tools/install) | sh
```

然而，由于某些原因，它对我不起作用，所以我手动操作。如果上面的一行程序对您也不起作用，请遵循下面的说明:

```
$ nix-shell --pure -p cacert cachix curl git jq nix[nix-shell:~/tmp]$ cachix use dappConfigured [https://dapp.cachix.org](https://dapp.cachix.org/) binary cache in /Users/detoo/.config/nix/nix.conf[nix-shell:~/tmp]$ git clone --recursive [https://github.com/dapphub/dapptools](https://github.com/dapphub/dapptools) $HOME/.dapp/dapptools[nix-shell:~/tmp]$ nix-env -f $HOME/.dapp/dapptools -iA dapp seth solc hevm ethsign...created 29 symlinks in user environment
```

# 取消交易

现在一切都准备好了，让我们尝试取消一个 tx。

假设你的钱包地址是`0xdeadbeefdeadbeefdeadbeefdeadbeefdeadbeef`，你要注销的 tx 的 nonce 是`123`，它的气价是`30 gwei`。我们要做的实际上是发送一个替换的**无操作** tx，具有**相同的随机数**和**更高的油价**，这实际上是通过用无操作覆盖原始 tx 来“取消”它。无操作 tx 的一个典型示例是向自己发送 0 乙醚，这将(1)什么都不改变,( 2)消耗非常少的汽油，这就是我们想要取消的。

要发送此类 tx，请运行以下命令:

```
$ seth --rpc-url=[https://](https://mainnet.infura.io/v3/b424ca737ba145588005367252bf1cee)url.to.your.endpoint send -F 0xdeadbeefdeadbeefdeadbeefdeadbeefdeadbeef 0xdeadbeefdeadbeefdeadbeefdeadbeefdeadbeef --value=0 --nonce=123 --gas-price=$(seth --to-wei 40 gwei)Ethereum account passphrase (not echoed):
```

`--rpc-url`应该是您从 Infura 获得的端点，或者如果您使用默认设置运行`geth`全节点，则应该是`[http://localhost:8545](http://localhost:8545)`。

请注意，我们发送的新 tx 的天然气价格比旧的价格至少高 10%,因此矿工更有可能接受它。

由于我们指定了从(`-F`)发送交易的帐户，`seth`将自动搜索`geth`密钥库中的钱包。如果您在前面的步骤中导入了`0xdeadbeefdeadbeefdeadbeefdeadbeefdeadbeef`的私钥，`seth`将会找到它，并提示您输入密码。输入您在导入过程中设置的密码，然后它将签名并发送 tx，并等待确认。一旦确认，取消就完成了！

# 有人说过可组合性吗？

CLI 的好处是您可以对它进行“编程”。比方说，如果你正在运行一个机器人，它卡住了不止一个，而是 10 个低气体交易。您可以编写一个简单的脚本来自动化这个过程，而不是运行该命令 10 次:

```
$ for i in {123..132}; do seth --rpc-url=[https://](https://mainnet.infura.io/v3/b424ca737ba145588005367252bf1cee)url.to.your.endpoint send -F 0xdeadbeefdeadbeefdeadbeefdeadbeefdeadbeef 0xdeadbeefdeadbeefdeadbeefdeadbeefdeadbeef --value=0 --nonce=$i --gas-price=$(seth --to-wei 40 gwei) --password=/path/to/your/password/file; done
```

注意额外的参数`--password`。我们希望这个过程是自动化的，可以通过在一个单独的文件`/path/to/your/password/file`中写入密码并指向`seth`来实现，这样它就不会每次都提示输入密码。

生产力和利润呢？

![](img/6b54c2c5229662d2136f2d7a8bc5a6c3.png)

Automated

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com?utm_source=coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://coincodecap.com?utm_source=coinmonks)