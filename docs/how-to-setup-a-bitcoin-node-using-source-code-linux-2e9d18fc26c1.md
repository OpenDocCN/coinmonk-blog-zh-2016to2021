# 如何使用源代码[Linux]设置一个比特币节点？

> 原文：<https://medium.com/coinmonks/how-to-setup-a-bitcoin-node-using-source-code-linux-2e9d18fc26c1?source=collection_archive---------4----------------------->

## 比特币节点有助于比特币开发或后端服务。

itcoin 是最受欢迎的加密货币，其价格在 2020 年持续上涨。很多开发者都在寻找设置比特币节点的指南。我第一次听到比特币这个词是在 2016 年**，在过去两年多的时间里，我一直致力于区块链开发。比特币代码是用`C++`写的。我们需要编译一个代码(如果你想对代码做一些改变，你可以这样做)，然后设置节点。因此，让我们来看看使用全新操作系统的步骤。我把这篇文章分成两部分。第一部分是编译源代码，第二部分是设置节点。**

**![](img/fcb6f8d802e266813a0847343371d966.png)**

**Photo by [André François McKenzie](https://unsplash.com/@silverhousehd?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)**

> **如果您只想设置一个节点，那么您可以跳过源代码编译过程，直接从[这里](https://bitcoin.org/bin/bitcoin-core-0.20.1/bitcoin-0.20.1-x86_64-linux-gnu.tar.gz)下载比特币可执行文件。提取文件并检查 bin 文件夹。**

# **第 1 部分:源代码编译指南**

****步骤一。****

**更新所有的库`apt-get update -y`，下载最新的比特币代码。你可以在这里得到[的所有发布。](https://github.com/bitcoin/bitcoin/releases)**

**`wget [https://github.com/bitcoin/bitcoin/archive/v0.20.1.tar.gz](https://github.com/bitcoin/bitcoin/archive/v0.20.1.tar.gz)`(如果没有找到 wget 命令，则`apt-get install wget -y`)**

****第二步。****

**提取文件`tar -xvzf v0.20.1.tar.gz`**

**安装一些助手库**

1.  **自动通信`apt-get install autoconf -y`**
2.  **Libtool `apt-get install -y libtool`**
3.  **G++ `apt-get install g++ -y`**
4.  **包装配置`apt-get install pkg-config -y`**
5.  **所有开发`apt-get install libboost-all-dev -y`**
6.  **LibDB 包`apt-get install libdb++-dev -y`**
7.  **实用工具`apt-get install bsdmainutils -y`**
8.  **制作必备`apt-get install build-essential -y`**

****第三步。****

**我们准备好编译代码了。进入提取的目录(比特币-0.20.1)并执行命令`./autogen.sh`**

**如果一切顺利，您将看到如下输出。**

```
root@05664050921e:~/bitcoin-0.20.1# ./autogen.shLlibtoolize: putting auxiliary files in AC_CONFIG_AUX_DIR, 'build-aux'.libtoolize: copying file 'build-aux/ltmain.sh'libtoolize: putting macros in AC_CONFIG_MACRO_DIRS, 'build-aux/m4'.libtoolize: copying file 'build-aux/m4/libtool.m4'libtoolize: copying file 'build-aux/m4/ltoptions.m4'etc...
```

****步骤四。****

**用不兼容的数据库版本配置项目，因为它只需要 4.8 版本，我们不需要跟踪依赖性。**

**`./configure --with-incompatible-bdb --disable-dependency-tracking`**

****第五步。****

**现在我们已经准备好使用`make`命令创建可执行文件。大约需要一个小时，所以不要担心。**

**[可选]您可以使用[文章](https://guptaarnish-it.medium.com/run-any-command-in-background-linux-unix-3ad3b6ed2138)在后台运行该命令。**

**成功执行 make 命令后，您可以检查 src 文件夹中是否创建了一些可执行文件。**

```
## These files are1\. bitcoind
2\. bitcoin-cli
3\. bitcoin-tx
4\. bitcoin-wallet
```

**恭喜你！！您已经编译了比特币代码，现在开始了设置比特币节点的新旅程。**

# **第 2 部分:设置比特币节点**

**我们需要一个比特币可执行文件，我们可以通过使用编译上述步骤的代码或直接从比特币[网站](https://bitcoin.org/bin/bitcoin-core-0.20.1/bitcoin-0.20.1-x86_64-linux-gnu.tar.gz)下载来获得。**

****步骤 1。****

**在根路径(`mkdir ~/.bitcoin`)中创建一个`.bitcoin`目录，然后进入该目录。现在我们必须编写我们的比特币节点的配置，这样我们就必须在`.bitcoin`目录中创建`bitcoin.conf`文件。**

****第二步。****

**要启动比特币节点，请执行以下命令:**

**`./bitcoind -daemon`如果我们想使用不赞成使用的 RPC 命令，那么我们必须编写`-deprecatedrpc=<rpc_package_name> (i.e. accounts)`**

> **如果我们想改变比特币目录，那么我们可以用`-datadir=<Folder_Path>`命令给出目录路径。我们可以查看`debug.log`文件中的日志。**

**用于停止比特币节点:`./bitcoin-cli stop`**

****第三步。****

**比特币 CLI 命令**

```
## RPC Command Verifycurl -XGET 'http://localhost:8332'Output: JSONRPC server handles only POST requests## It means our bitcoin node working successfully and RPC is ready ## to use.#### ------ Some bitcoin CLI commands ------- ## To check bitcoin wallet balance
./bitcoin-cli getbalance## Check bitcoin fee for ECONOMICAL (regular) confirmation
./bitcoin-cli estimatesmartfee 1## Check bitcoin fee for CONSERVATIVE (fastest) confirmation
./bitcoin-cli estimatesmartfee 6## Check complete wallet information
./bitcoin-cli getwalletinfo
```

> **你可以在这里找到[的所有 CLI 命令。](https://en.bitcoin.it/wiki/Original_Bitcoin_client/API_calls_list)**

**[](https://guptaarnish-it.medium.com/how-does-the-bitcoin-seed-phrase-work-b4de10bc275b) [## 比特币种子短语是如何工作的？

### 种子短语是比特币钱包的关键。

guptaarnish-it.medium.com](https://guptaarnish-it.medium.com/how-does-the-bitcoin-seed-phrase-work-b4de10bc275b)** 

**是哇哇。最后，我们取得了成功。如果你有任何问题或疑问，你可以写在评论框里，我会尽力解答你的疑问，并期待你的反馈。**

**如果你喜欢这篇文章，给我一秒钟鼓掌。**

**感谢您的阅读。过一个比特币的日子。**

## **另外，阅读**

*   **最好的[密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)**
*   **[德里比特评论](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和 Testnet**
*   **[FTX 密码交易所评论](/coinmonks/ftx-crypto-exchange-review-53664ac1198f)**
*   **[Bybit 交换审查](/coinmonks/bybit-exchange-review-dbd570019b71)**
*   **最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)**
*   **[密码本交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)**
*   **最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)**
*   **[最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)**
*   **最佳[加密借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)**
*   **[莱杰纳米 S vs 特雷佐 one vs 特雷佐 T vs 莱杰纳米 X](https://blog.coincodecap.com/ledger-nano-s-vs-trezor-one-ledger-nano-x-trezor-t)**
*   **[block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)vs Hodlnaut**
*   **Bitsgap 评论——一个轻松赚钱的加密交易机器人**
*   **为专业人士设计的加密交易机器人**
*   **[PrimeXBT 审查](/coinmonks/primexbt-review-88e0815be858) |杠杆交易、费用和交易**
*   **HaasOnline 评论享受九折优惠**
*   **Bitmex 的[保证金交易的白痴指南](/coinmonks/the-idiots-guide-to-margin-trading-on-bitmex-dbbd7742c6fc?source=friends_link&sk=7bfa99d2a181142510c8442c8ddb0786)**
*   **[eToro 评论](/coinmonks/etoro-review-78807ddeb33c) |交易股票、密码、交易所交易基金、差价合约和商品**
*   **[BlockFi 评论](/coinmonks/blockfi-review-53096053c097) |从您的密码中赚取高达 8.6%的利息**
*   **[面向开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)**
*   **[最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)**
*   **[加密套利](/coinmonks/crypto-arbitrage-guide-how-to-make-money-as-a-beginner-62bfe5c868f6)指南:新手如何赚钱**
*   **顶级[比特币节点](https://blog.coincodecap.com/bitcoin-node-solutions)提供商**
*   **最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)**
*   **了解比特币的[最佳书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)**

> **[直接在您的收件箱中获得最佳软件交易](/coinmonks/newsletters/coinmonks)**

**[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://medium.com/coinmonks/newsletters/coinmonks)**