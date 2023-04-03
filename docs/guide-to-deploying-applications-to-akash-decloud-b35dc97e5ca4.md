# 将应用部署到 Akash DeCloud 的指南

> 原文：<https://medium.com/coinmonks/guide-to-deploying-applications-to-akash-decloud-b35dc97e5ca4?source=collection_archive---------1----------------------->

## 学习在分散的云上部署任何容器化的应用程序

![](img/c4af6b365a3632a939377f3fc1325726.png)

Photo by [Edurne Chopeitia](https://unsplash.com/@edurnepaula?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

我最近在区块链发现了一个很有前途的传统云平台的替代品，叫做 Akash。 [Akash Network](https://akash.network/) 正在构建第一个分散式云(DeCloud)计算市场，它使任何具有未使用计算周期的计算机都可以成为云提供商，并允许开发人员以远低于(据称高达 10 倍！)由当前市场提供商(如 AWS、Google Cloud 和 Microsoft Azure)提供的那些。如果你不熟悉去中心化云(DeCloud)或 Akash，你可以看看他们的[网站](https://akash.network/)，他们的[白皮书](https://akash.network/whitepapers/)，他们的[博客](https://akash.network/blog/)，以及[其他博客](/crypto/youre-not-decentralized-if-you-re-relying-on-amazon-or-microsoft-for-hosting-4f0c74471b17)。这个项目仍然相当年轻，但是他们最近的[阿卡什安挑战 3](https://akash.network/challenge/) 展示了他们部署平台的一个令人印象深刻的测试版本。

在本指南中(基于[官方文档](https://docs.akash.network/guides/deploy))，我将向您展示将容器化的应用程序部署到 Akash 是多么容易。正如您将看到的，最困难的部分是编写和封装您的应用程序，无论您选择哪个云提供商，您都可能会这样做。经过一次设置后，部署到 Akash 只需要编写一个小的配置文件并执行几个命令。

# 先决条件

本指南应该适用于 Mac 用户、(大多数)Linux 用户，而*可能*适用于 Windows 用户(参见[支持的平台](https://github.com/ovrclk/akash))。本指南还假设了基本的命令行舒适性和对 [Docker](https://docs.docker.com/get-docker/) 的一些熟悉。

本指南中使用的 Docker 镜像可以在用户名`wlouie1`下[这里](https://hub.docker.com/r/wlouie1/ufo-data-vis)获得，但是如果你想自己创建一个，你需要安装 [Docker](https://docs.docker.com/get-docker/) 并在 [Docker Hub](https://hub.docker.com/) 上拥有一个账户。

要部署到 Akash DeCloud，您需要:

1.  [安装阿卡什](https://docs.akash.network/guides/install)。
2.  [选择您的阿卡什网络](https://docs.akash.network/guides/version)。我们将使用 *edgenet* ，这是一个测试网，其部署能力将反映他们的下一个 Mainnet 版本。
3.  [向您的账户](https://docs.akash.network/guides/funding)注入阿卡什代币(Akt)来支付部署费用。对于 edgenet，Akash 提供了一个 AKT 水龙头来资助测试部署。

# 容器化您的应用程序

*(此处* *提供了本节中描述的应用程序的 Docker 映像，因此可以直接跳到下面的* ***部署到 Akash*** *一节。)*

## 编写应用程序

出于演示目的，我们将使用[这个 web 应用程序](https://github.com/wlouie1/UFO-Sightings)来探索世界各地的 UFO 目击事件:

![](img/b3919292a5c72bce1c609119c33432dd.png)

UFO Sightings Around the World, 1906–2014\. [Image by Author](https://wilsonlouie.medium.com/)

通过以下方式克隆应用程序:

```
git clone [https://github.com/wlouie1/UFO-Sightings.git](https://github.com/wlouie1/UFO-Sightings.git)
cd UFO-Sightings/
```

如您所见，该应用程序完全由静态 HTML/Javascript/CSS 文件组成。如果您要在本地启动服务器，例如，如果您安装了 Python 3，则使用以下命令:

```
python -m http.server 8000
```

然后您可以导航到`[http://localhost:8000](http://localhost:8000)`并查看应用程序。

## 用码头工人集装箱化

因为我们所需要的只是一个服务器来服务我们 web 应用程序的静态文件，所以我们可以创建一个简单的 Docker 映像，将我们的静态文件与 web 服务器捆绑在一起，比如 [Nginx](https://nginx.org/en/) 。

在`UFO-Sightings`目录下，创建一个名为`Dockerfile`的文件，内容如下:

```
**FROM** nginx:alpine
**COPY** . /usr/share/nginx/html
```

构建 Docker 映像(为了简单起见，我们将其命名为`ufo-data-vis`并标记为`latest`；[小心实践中的“最新”标签，尽管](https://vsupalov.com/docker-latest-tag/))通过:

```
docker build -t ufo-data-vis:latest .
```

要测试它，将映像作为容器运行，将主机的端口 80 转发到容器的端口 80(Nginx 的默认端口):

```
docker run -p 80:80 ufo-data-vis:latest
```

如果一切正常，你应该可以在`[http://localhost:80](http://localhost:8000)`查看申请。

## 将图像推送到 Docker 注册表

一旦我们有了一个构建好的 Docker 映像，我们需要让它公开可用，这样 Akash 部署过程就可以提取映像(私有容器支持在他们的[路线图](https://github.com/orgs/ovrclk/projects/2#card-28484701)中)。如果您有一个用户名为`USERNAME`的 Docker Hub 帐户，创建一个名为`ufo-data-vis`的存储库，然后使用以下内容标记并推送图像:

```
docker tag ufo-data-vis:latest USERNAME/ufo-data-vis:latest
docker push USERNAME/ufo-data-vis:latest
```

..我们完事了。代表我们的容器化应用程序的 Docker 映像现在是公开可用的，我们可以继续部署到 Akash。

## 考虑 Docker 合成文件

部署到 Akash 包括编写一个定义部署配置的栈定义语言(SDL)文件(更多细节在下面的**创建部署配置**部分)。SDL 文件在这里是。你可能会注意到 SDL 格式与 Docker 合成文件非常相似！

如果我们要构建一个 Docker 合成文件，利用我们的 web 应用程序的公共 Docker 图像，它看起来会像这样:

```
**version**: "3.8"
**services**:
  **web**:
    **image**: USERNAME/ufo-data-vis:latest
    **ports**:
      - "80:80"
```

为了验证，您可以将这个文件保存为`UFO-Sightings`目录下的`docker-compose.yml`，并使用 docker-compose 运行容器(确保停止任何先前运行的容器):

```
docker-compose up --build
```

并导航至`[http://localhost:80](http://localhost:8000)`进行确认。

正如您将在**创建部署配置**一节中看到的，编写 SDL 文件实质上变成了直接翻译。

# 部署到阿卡什

在编写和封装您的应用程序之后，您就完成了最难的部分！除了一些一次性设置之外，部署到 Akash 只需要编写一个小的配置文件并执行几个命令。

## 环境设置

使用`export VARNAME=…`设置以下外壳变量:

*   `AKASH_NODE` : Akash 网络配置基础 URL。见[此处](https://docs.akash.network/guides/version)。
*   `AKASH_CHAIN_ID`:连接到的阿卡什网络的链 ID。见[此处](https://docs.akash.network/guides/version)。
*   `ACCOUNT_ADDRESS`:您的账户地址。见[此处](https://docs.akash.network/guides/wallet#account-address)。
*   `KEY_NAME`:您将要部署的密钥的名称。如果您尚未设置密钥，请参见此处的。

您可以通过运行以下命令来检查您的帐户是否有足够的余额:

```
akash query bank balances --node **$AKASH_NODE** **$ACCOUNT_ADDRESS**
```

您应该会看到类似以下内容的响应:

```
balances:
- **amount: "93000637"**
  **denom: uakt**
pagination:
  next_key: null
  total: "0"
```

请注意，所显示的余额是以 10^-6).为单位的在上面的例子中，账户余额为 93 AKT 。我们现在开始部署。

## 创建部署配置

部署服务、数据中心、定价等。，由使用[栈定义语言](https://docs.akash.network/documentation/sdl) (SDL)的 [YAML](http://www.yaml.org/start.html) 配置文件描述。编写这个配置文件非常类似于编写一个 Docker Compose 文件加上一些额外的东西。对于我们的 web 应用程序，一个 SDL 可能看起来像这样(参见上面的**考虑 Docker 合成文件**一节中类似的 Docker 合成文件):

```
---
**version**: "2.0"**services**:
  **web**:
    **image**: USERNAME/ufo-data-vis:latest
    **expose**:
      - **port**: 80
        **as**: 80
        **to**:
          - **global**: true**profiles**:
  **compute**:
    **web**:
      **resources**:
        **cpu**:
          **units**: 0.1
        **memory**:
          **size**: 512Mi
        **storage**:
          **size**: 512Mi
  **placement**:
    **westcoast**:
      **attributes**:
        **organization**: ovrclk.com
      **signedBy**:
        **anyOf**:
          - "akash1vz375dkt0c60annyp6mkzeejfq0qpyevhseu05"
      **pricing**:
        **web**: 
          **denom**: uakt
          **amount**: 1000**deployment**:
  **web**:
    **westcoast**:
      **profile**: web
      **count**: 1
```

上面指定了`services`下类似 Docker Compose 的规范(在我们的例子中，我们只有一个名为`web`的工作负载，它构建我们的 web 应用程序映像)、在`profiles.compute`下为每个服务分配多少计算资源、`profiles.placement`下命名的数据中心配置文件，以及在`deployment`下数据中心配置文件和计算配置文件之间的映射。

将该文件另存为`deploy.yml`。只需几个命令，应用程序就可以部署了！

## 部署！

部署的语法是`akash tx deployment create <config-path> --from <key-name>`。更具体地说，在我们的案例中:

```
akash tx deployment create **deploy.yml** --from **$KEY_NAME** --node **$AKASH_NODE** --chain-id **$AKASH_CHAIN_ID** -y
```

这会发布您的部署，并触发 Akash marketplace 尝试通过拍卖为您匹配一个提供商。这可能需要几秒钟。您可以通过运行以下命令来检查租赁状态:

```
akash query market lease list --owner **$ACCOUNT_ADDRESS** --node **$AKASH_NODE** --state active
```

一旦您与提供商匹配，您应该会看到类似于以下内容的响应:

```
- lease_id:
    **dseq**: "19553"
    **gseq**: 1
    **oseq**: 1
    owner: akash1j8s87w3fctz7nlcqtkl5clnc805r240443eksx
    **provider**: akash15ql9ycjkkxhpc2nxtnf78qqjguwzz8gc4ue7wl
  price:
    amount: "186"
    denom: uakt
  state: active
pagination:
  next_key: null
  total: "0"
```

在上面的示例中，租约是使用每块 0.00000186 AKT 来执行容器而创建的。

为了方便和清楚起见，根据上述步骤返回的租约信息，将下面的一组值提取到 shell 变量中:

*   `PROVIDER` : `akash15ql9ycjkkxhpc2nxtnf78qqjguwzz8gc4ue7wl`在我们上面的例子中。
*   `DSEQ` : `19553`在我们上面的例子中。
*   `OSEQ` : `1`在我们上面的例子中。
*   `GSEQ` : `1`在我们上面的例子中。

获得租约后的最后一步是开始在提供商机器上构建映像。这是通过以下方式上传清单来完成的:

```
akash provider send-manifest **deploy.yml** --node **$AKASH_NODE** --dseq **$DSEQ** --oseq **$OSEQ** --gseq **$GSEQ** --owner **$ACCOUNT_ADDRESS** --provider **$PROVIDER**
```

当您的映像在提供者机器上构建时，您可以查看应用程序日志来观察进度或进行调试:

```
akash provider service-logs --node **$AKASH_NODE** --dseq **$DSEQ** --oseq **$OSEQ** --gseq **$GSEQ** --provider **$PROVIDER** --owner **$ACCOUNT_ADDRESS** --service **$SERVICE_NAME**
```

其中`$SERVICE_NAME`是您的 SDL 中定义的服务的名称。在我们这里，这是`web`。

最后，您可以通过运行以下命令来检索访问详细信息:

```
akash provider lease-status --node **$AKASH_NODE** --dseq **$DSEQ** --oseq **$OSEQ** --gseq **$GSEQ** --provider **$PROVIDER** --owner **$ACCOUNT_ADDRESS**
```

这将给出类似于以下内容的响应:

```
{
  "services": {
    "web": {
      "name": "web",
      "available": 1,
      "total": 1,
      **"uris": [
        "6veev7chcfmnclgqklegcc.provider4.akashdev.net"
      ],**
      "observed-generation": 0,
      "replicas": 0,
      "updated-replicas": 0,
      "ready-replicas": 0,
      "available-replicas": 0
    }
  },
  "forwarded-ports": {}
}
```

在上面的例子中，在`uris`下，我们的应用程序可以在[http://6veev7chcfmnclgqklegcc.provider4.akashdev.net](http://6veev7chcfmnclgqklegcc.provider4.akashdev.net)访问。尝试导航到您在`uris`下看到的任何链接，您应该会看到我们的 web 应用程序！

## 关闭部署

关闭您的部署意味着您的容器将被取消供应，令牌传输将停止。如果您想要关闭部署，请通过以下方式创建一个`deployment-close`事务:

```
akash tx deployment close --node **$AKASH_NODE** --chain-id **$AKASH_CHAIN_ID** --dseq **$DSEQ** --owner **$ACCOUNT_ADDRESS** --from **$KEY_NAME** -y
```

您可以查询市场以检查您的租赁是否成功结束:

```
akash query market lease list --owner **$ACCOUNT_ADDRESS** --node **$AKASH_NODE**
```

这将返回类似于以下内容的响应:

```
leases:
- lease_id:
    dseq: "19553"
    gseq: 1
    oseq: 1
    owner: akash1j8s87w3fctz7nlcqtkl5clnc805r240443eksx
    provider: akash15ql9ycjkkxhpc2nxtnf78qqjguwzz8gc4ue7wl
  price:
    amount: "186"
    denom: uakt
  **state: closed**
pagination:
  next_key: null
  total: "0"
```

如上图所示，您的租约应标记为`state: closed`。

# 结论

如前所述，将应用程序部署到 Akash 相当简单。由于这是 Akash 部署平台的测试版，我们可以期待[更多的功能](https://github.com/orgs/ovrclk/projects/2)和未来进一步简化的工作流程。

如果你有兴趣在 Akash 上了解机器学习，也请查看我的其他系列( [**第 1 部分**](https://wilsonlouie.medium.com/machine-learning-on-akash-decloud-part-1-3-training-cnn-on-mnist-using-tensorflow-be464f0f067e) 、 [**第 2 部分**](https://wilsonlouie.medium.com/machine-learning-on-akash-decloud-part-2-3-tensorflow-model-serving-12e30d77a156) 和 [**第 3 部分**](https://wilsonlouie.medium.com/machine-learning-on-akash-decloud-part-3-3-deploying-a-deep-learning-web-application-6a9e71e71dd1) )，详细介绍我在 Akash 上进行的训练、服务和部署深度学习 web 应用程序的实验！

## 另外，阅读

*   [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [3 商业评论](/coinmonks/3commas-review-an-excellent-crypto-trading-bot-2020-1313a58bec92)
*   [AAX 交易所评论](/coinmonks/aax-exchange-review-2021-67c5ea09330c) |推荐代码、交易费用、利弊
*   [Deribit 审查](/coinmonks/deribit-review-options-fees-apis-and-testnet-2ca16c4bbdb2) |选项、费用、API 和 Testnet
*   FTX 密码交易所评论
*   [Bybit 交换审查](/coinmonks/bybit-exchange-review-dbd570019b71)
*   [3Commas vs Cryptohopper](/coinmonks/cryptohopper-vs-3commas-vs-shrimpy-a2c16095b8fe)
*   最好的比特币[硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069?source=friends_link&sk=324dd9ff8556ab578d71e7ad7658ad7c)
*   [密码本交易平台](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)
*   [bits gap vs 3 commas vs quad ency](https://blog.coincodecap.com/bitsgap-3commas-quadency)
*   最好的[加密税务软件](/coinmonks/best-crypto-tax-tool-for-my-money-72d4b430816b)
*   [最佳加密交易平台](/coinmonks/the-best-crypto-trading-platforms-in-2020-the-definitive-guide-updated-c72f8b874555)
*   最佳[加密借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [莱杰 Nano S vs 特雷佐 one vs 特雷佐 T vs 莱杰 Nano X](https://blog.coincodecap.com/ledger-nano-s-vs-trezor-one-ledger-nano-x-trezor-t)
*   [block fi vs Celsius](/coinmonks/blockfi-vs-celsius-vs-hodlnaut-8a1cc8c26630)vs Hodlnaut
*   [Bitsgap 评论](/coinmonks/bitsgap-review-a-crypto-trading-bot-that-makes-easy-money-a5d88a336df2)——一个轻松赚钱的加密交易机器人
*   [Quadency Review](/coinmonks/quadency-review-a-crypto-trading-automation-platform-3068eaa374e1) -专为专业人士打造的加密交易机器人
*   [PrimeXBT 审查](/coinmonks/primexbt-review-88e0815be858) |杠杆交易、费用和交易
*   [Altrady 审查](https://blog.coincodecap.com/altrady-reivew)
*   [埃利帕尔泰坦评论](/coinmonks/ellipal-titan-review-85e9071dd029)
*   [SecuX Stone 评论](https://blog.coincodecap.com/secux-stone-hardware-wallet-review)
*   [BlockFi 评论](/coinmonks/blockfi-review-53096053c097) |从您的密码中赚取高达 8.6%的利息
*   [开发人员的最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   [最佳区块链分析工具](https://bitquery.io/blog/best-blockchain-analysis-tools-and-software)
*   加密套利指南:新手如何赚钱
*   顶级[比特币节点](https://blog.coincodecap.com/bitcoin-node-solutions)提供商
*   最佳[加密制图工具](/coinmonks/what-are-the-best-charting-platforms-for-cryptocurrency-trading-85aade584d80)
*   了解比特币的[最佳书籍有哪些？](/coinmonks/what-are-the-best-books-to-learn-bitcoin-409aeb9aff4b)

> [直接在您的收件箱中获得最佳软件交易](/coinmonks/newsletters/coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://medium.com/coinmonks/newsletters/coinmonks)