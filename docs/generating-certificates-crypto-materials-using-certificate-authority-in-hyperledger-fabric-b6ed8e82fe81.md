# 使用 Hyperledger 结构中的证书颁发机构生成证书(加密材料)

> 原文：<https://medium.com/coinmonks/generating-certificates-crypto-materials-using-certificate-authority-in-hyperledger-fabric-b6ed8e82fe81?source=collection_archive---------0----------------------->

在这个故事中，我们将使用 certificate authority 为 Hyperledger 结构网络创建所有必要的证书。

git Repo:[https://github . com/adhavpavan/generate certificate using ca-hyperledger fabric . git](https://github.com/adhavpavan/GenerateCertificateUsingCA-HyperledgerFabric.git)

使用以下配置创建`docker-compose-ca.yaml`文件。

![](img/de1bff747edfc2ce4d12166a07860057.png)

创建一个文件夹结构如下，目前，我们没有任何组织文件夹内。

![](img/3b79f489438cc00e4759afb045fd1b10.png)

我们需要为每个组织创建一个如下所示的文件夹结构。

> [发现并回顾最佳区块链软件](https://coincodecap.com)

**组织层级结构
1)同级组织**

![](img/a1df2b2522805991034938092f56be9d.png)

2) **订购者组织**

![](img/31ae9a241b328ab6727e996f6af345b4.png)

3) **用户结构**

![](img/29a5c0562b9c42434c3a83f0513826f1.png)

**4) MSP 结构**

![](img/0e2570d6b08c766805f84996e90ff8a6.png)

通常，创建证书的过程是
1)注册
2)注册

注册是用户从给定的 CA 请求并获得数字证书的过程。注册通常由注册员完成，告诉 CA 发行数字证书。

*   一名**管理员**(注册员)在 CA 中注册。然后**管理员**接收该**管理员**的签名密钥和证书。
*   管理员然后用正确的信息将用户 1 注册到 CA 中。CA 返回一个**秘密**。
*   然后，该密码用于将用户 1 注册到 CA。结果是**用户 1** 的签名密钥和证书。

让我们启动一个证书颁发机构服务。因为我们已经创造了`docker-compose-ca.yaml`。我们已经在`docker-compose`文件中绘制了卷。

`volumes:
- ./organizations/fabric-ca/org1:/etc/hyperledger/fabric-ca-server`

提供了四个 env 变量。请确保您已经提供了所有这些信息。
`- FABRIC_CA_HOME=/etc/hyperledger/fabric-ca-server
- FABRIC_CA_SERVER_CA_NAME=ca-org1
- FABRIC_CA_SERVER_TLS_ENABLED=true
- FABRIC_CA_SERVER_PORT=7054`

运行以下命令

`docker compose -f docker-compose-cli up`

当我们启动 CA 容器时，我们可以看到下面的详细日志。更多详情，请查看下面的日志。

请通过运行`docker ps`确保 ca 容器正在运行，您可以看到 ca 服务的以下输出

![](img/91c423110870a7d7f317f4968d26b44c.png)

当 CA 服务启动并运行时，我们可以在 organization 文件夹中看到下面的文件夹结构，我们已经在`docker-compose-ca.yaml`文件中映射了该结构

![](img/af6fdae78ae82719fe35e3718dbcd2c2.png)

**步骤 1** :注册 CA 管理员
运行下面的命令
`mkdir -p organizations/peerOrganizations/org1.example.com/`创建目录

`fabric-ca-client enroll -u [https://admin:adminpw@localhost:7054](https://admin:adminpw@localhost:7054) — caname ca-org1 — tls.certfiles ${PWD}/organizations/fabric-ca/org1/tls-cert.pem`

一旦我们注册了 Admin，我们将获得以下管理 MPS。请转到特定文件夹进行交叉检查。(在我的例子中，它是:/home/pavan/。结构-ca-客户端/msp)

![](img/eb7a16cc5e73b24d4ca43ebed2c7f90f.png)

# 组织单位

为了配置该 MSP 的有效成员应该在其 X.509 证书中包含的组织单元列表，`config.yaml`文件需要指定组织单元(简称 OU)标识符。你可以在下面找到一个例子:

```
OrganizationalUnitIdentifiers:
  **-** Certificate: "cacerts/cacert1.pem"
    OrganizationalUnitIdentifier: "commercial"
  **-** Certificate: "cacerts/cacert2.pem"
    OrganizationalUnitIdentifier: "administrators"
```

上面的例子声明了两个组织单位标识符:**商业**和**管理员**。如果 MSP 标识至少带有这些组织单位标识符中的一个，则它是有效的。`Certificate`字段指的是 CA 或中间 CA 证书路径，在该路径下，应该验证具有特定 OU 的身份。该路径相对于 MSP 根文件夹，不能为空。

# 身份分类

默认的 MSP 实现允许组织根据 x509 证书的 ou 将身份进一步分类为客户端、管理员、对等用户和订购者。

*   如果一个身份在网络上进行交易，它应该被归类为**客户端**。
*   如果某个身份处理管理任务，如将对等体加入通道或签署通道配置更新事务，则该身份应归类为**管理员**。
*   如果一个身份签署或提交事务，它应该被分类为**对等体**。
*   如果一个身份属于一个排序节点，它应该被分类为一个**排序者**。

为了定义给定 MSP 的客户机、管理员、对等机和订购者，需要适当地设置文件的`config.yaml`。您可以在下面的`config.yaml`文件中找到一个示例 NodeOU 部分:

```
NodeOUs:
  Enable: true
  ClientOUIdentifier:
    Certificate: "cacerts/cacert.pem"
    OrganizationalUnitIdentifier: "client"
  AdminOUIdentifier:
    Certificate: "cacerts/cacert.pem"
    OrganizationalUnitIdentifier: "admin"
  PeerOUIdentifier:
    Certificate: "cacerts/cacert.pem"
    OrganizationalUnitIdentifier: "peer"
  OrdererOUIdentifier:
    Certificate: "cacerts/cacert.pem"
    OrganizationalUnitIdentifier: "orderer"
```

当`NodeOUs.Enable`设置为`true`时，身份分类启用。然后，通过设置`NodeOUs.ClientOUIdentifier` ( `NodeOUs.AdminOUIdentifier`、`NodeOUs.PeerOUIdentifier`、`NodeOUs.OrdererOUIdentifier`)键的属性来定义客户端(管理、对等、订购方)组织单位标识符:

1.  `OrganizationalUnitIdentifier`:x509 证书需要包含的 OU 值，它被认为是一个客户端(分别为 admin、peer、orderer)。如果此字段为空，则分类不适用。
2.  `Certificate`:将此设置为 CA 或中间 CA 证书的路径，在该路径下应验证客户端(对等、管理员或订购者)身份。该字段相对于 MSP 根文件夹。该字段是可选的。您可以将此字段留空，并允许在 MSP 配置中定义的任何 CA 下验证证书。

注意，如果`NodeOUs.ClientOUIdentifier`部分(`NodeOUs.AdminOUIdentifier`、`NodeOUs.PeerOUIdentifier`、`NodeOUs.OrdererOUIdentifier`)缺失，则分类不适用。如果`NodeOUs.Enable`设置为`true`并且没有定义分类关键字，则认为身份分类被禁用。

身份可以使用组织单位来分类为客户、管理员、同事或订购者。这四种分类是相互排斥的。在将身份分类为客户端或对等体之前，需要启用 1.1 通道功能。要将身份分类为管理员或订购者，需要启用 1.4.3 通道功能。

分类允许将身份归类为管理员(并执行管理员操作)，而无需将证书存储在 MSP 的`admincerts`文件夹中。相反，`admincerts`文件夹可以保持为空，可以通过向管理员 OU 注册身份来创建管理员。文件夹`admincerts`中的证书仍然将管理员的角色授予其持有者，只要他们拥有客户端或管理员 OU。

注:以上部分摘自官方文件，以了解更多详情。

我们必须在`organizations/peerOrganizations/org1.example.com/msp/config.yaml`创建 config.yaml 文件，并删除下面的代码。

```
NodeOUs:
  Enable: true
  ClientOUIdentifier:
    Certificate: "cacerts/cacert.pem"
    OrganizationalUnitIdentifier: "client"
  AdminOUIdentifier:
    Certificate: "cacerts/cacert.pem"
    OrganizationalUnitIdentifier: "admin"
  PeerOUIdentifier:
    Certificate: "cacerts/cacert.pem"
    OrganizationalUnitIdentifier: "peer"
  OrdererOUIdentifier:
    Certificate: "cacerts/cacert.pem"
    OrganizationalUnitIdentifier: "orderer" 
```

步骤 2:注册 peer0

`fabric-ca-client register — caname ca-org1 — id.name peer0 — id.secret peer0pw — id.type peer — id.attrs ‘“hf.Registrar.Roles=peer”’ — tls.certfiles ${PWD}/organizations/fabric-ca/org1/tls-cert.pem`

在每个步骤中，请确保生成必要的证书。当我们运行这些命令时，我们可以在 terminal/shell/cmd 行看到生成的文件位置。

步骤 3:注册用户

`fabric-ca-client register — caname ca-org1 — id.name user1 — id.secret user1pw — id.type client — id.attrs ‘“hf.Registrar.Roles=client”’ — tls.certfiles ${PWD}/organizations/fabric-ca/org1/tls-cert.pem`

步骤 3:注册组织管理员

`fabric-ca-client register — caname ca-org1 — id.name org1admin — id.secret org1adminpw — id.type admin — id.attrs ‘“hf.Registrar.Roles=admin”’ — tls.certfiles ${PWD}/organizations/fabric-ca/org1/tls-cert.pem`

步骤 4:创建以下目录

`mkdir -p organizations/peerOrganizations/org1.example.com/peers`

`mkdir -p organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com`

步骤 5:生成 peer0 MSP

`fabric-ca-client enroll -u [https://peer0:peer0pw@localhost:7054](https://peer0:peer0pw@localhost:7054) — caname ca-org1 -M ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp — csr.hosts peer0.org1.example.com — tls.certfiles ${PWD}/organizations/fabric-ca/org1/tls-cert.pem`

步骤 6:将配置文件复制到新生成的对等 MSP 文件夹中

`cp ${PWD}/organizations/peerOrganizations/org1.example.com/msp/config.yaml ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp/config.yaml`

步骤 7:生成 peer0-tls 证书

`fabric-ca-client enroll -u [https://peer0:peer0pw@localhost:7054](https://peer0:peer0pw@localhost:7054) — caname ca-org1 -M ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls — enrollment.profile tls — csr.hosts peer0.org1.example.com — csr.hosts localhost — tls.certfiles ${PWD}/organizations/fabric-ca/org1/tls-cert.pem`

无论`tlscerts, signcerts,keystore` 创造了什么，我们都必须像下面这样移动 tls

`cp ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/tlscacerts/* ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt`

`cp ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/signcerts/* ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/server.crt`

`cp ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/keystore/* ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/server.key`

`mkdir ${PWD}/organizations/peerOrganizations/org1.example.com/msp/tlscacerts`

`cp ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/tlscacerts/* ${PWD}/organizations/peerOrganizations/org1.example.com/msp/tlscacerts/ca.crt`

`mkdir ${PWD}/organizations/peerOrganizations/org1.example.com/tlsca`

`cp ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/tlscacerts/* ${PWD}/organizations/peerOrganizations/org1.example.com/tlsca/tlsca.org1.example.com-cert.pem`

`mkdir ${PWD}/organizations/peerOrganizations/org1.example.com/ca`

`cp ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp/cacerts/* ${PWD}/organizations/peerOrganizations/org1.example.com/ca/ca.org1.example.com-cert.pem`

步骤 8:生成用户证书

创建以下目录。

`mkdir -p organizations/peerOrganizations/org1.example.com/users`

`mkdir -p organizations/peerOrganizations/org1.example.com/users/User1@org1.example.com`

步骤 9:生成用户 MSP

`fabric-ca-client enroll -u [https://user1:user1pw@localhost:7054](https://user1:user1pw@localhost:7054) — caname ca-org1 -M ${PWD}/organizations/peerOrganizations/org1.example.com/users/User1@org1.example.com/msp — tls.certfiles ${PWD}/organizations/fabric-ca/org1/tls-cert.pem`

第 10 步:管理员用户

`mkdir -p organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com`

步骤 11:生成组织管理 MSP

`fabric-ca-client enroll -u [https://org1admin:org1adminpw@localhost:7054](https://org1admin:org1adminpw@localhost:7054) — caname ca-org1 -M ${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp — tls.certfiles ${PWD}/organizations/fabric-ca/org1/tls-cert.pem`

步骤 12:将`config.yaml`文件复制到新创建的 MSP 文件夹中

`cp ${PWD}/organizations/peerOrganizations/org1.example.com/msp/config.yaml ${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp/config.yaml`

这是为 org1 创建加密材料的过程，您也可以为 Org2 和订购者遵循相同的过程。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)