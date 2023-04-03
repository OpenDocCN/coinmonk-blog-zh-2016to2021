# 从头开始创建 Hyperledger 结构网络(1.4 版)

> 原文：<https://medium.com/coinmonks/creating-a-hyperledger-fabric-network-from-scratch-v-1-4-695906dbb7c6?source=collection_archive---------0----------------------->

由于互联网上关于 hyperledger fabric 网络设置的教程并不多，而且几乎所有的教程都解释了第一个网络或将其作为参考，因此在本教程中，**我将向大家展示如何从头开始创建 hyperledger fabric 网络！** *只有了解 hyperledger fabric 的架构，才能按照本教程学习。*

> 注意:这是我的第一篇文章，所以欢迎任何建议或更正。

# 先决条件:-

1.  hyperledger fabric 架构的基础知识
2.  docker-compose 知识
3.  码头工人。按照中的[进行安装。](https://docs.docker.com/install/)
4.  docker-撰写。按照中的[进行安装。](https://docs.docker.com/compose/install/)
5.  OpenSSL。它预装在大多数 Linux 发行版上，Windows 用户遵循[这个](https://tecadmin.net/install-openssl-on-windows/)
6.  Hyperledger 结构二进制文件:-

```
$ curl -L -o bin.tar.gz  "[https://www.dropbox.com/s/1o8xwur74z176ci/bin.tar.gz?dl=1](https://www.dropbox.com/s/1o8xwur74z176ci/bin.tar.gz?dl=1)"$ tar -xvf bin.tar.gz
$ sudo mv bin/configtxgen /usr/local/bin
$ sudo mv bin/fabric-ca-client /usr/local/bin
$ sudo rm -rf bin/
```

# 网络概述

我将尽可能保持简单，因为主要目的是教你基础知识，从零开始创建一个网络，然后可以扩展到创建更复杂的网络。

该网络将包括:-

1.  1 个 ca 服务器，用于所有 MSP 生成证书。
2.  1 台 tls ca 服务器，用于生成所有节点和客户端用于 tls 通信的证书。

3.同一个组织名称“org0”下的 3 个木筏订购者。

4.“org1”的 1 个对等体。

5.1 个 cli 节点为 *org1 创建通道。*

> 注意:我们可以使用同一个 ca 服务器来生成两种证书，但这不是一个好的做法

# 什么是 MSP？

**根据总账结构文档:-**

> 成员资格服务提供商(MSP)是一个 Hyperledger Fabric 组件，提供成员资格操作的抽象。
> 具体而言，MSP 抽象出发布证书、验证证书和用户认证背后的所有加密机制和协议。MSP 可以定义自己的身份概念，以及管理(身份验证)和认证(签名生成和验证)这些身份的规则。

我会用更基本的方式来解释。假设一个结构网络正在运行，并且您想要加入一个新的对等网络。网络如何知道对等体是否可以加入它？
Ans。它知道这一点，因为我们在网络设置期间在 genesis 块中传递了该信息(当然，我们可以在运行时更改它)。在 [byfn](https://hyperledger-fabric.readthedocs.io/en/release-1.4/build_network.html) 教程中，configtx.yaml 包含组织部分:-

我们在这里定义 Org1 MSP 以及其他组织，基本上，主要的东西是 **MSPDir，**这个目录包含:- *admincerts* ， *cacerts* ， *tlscacerts。*因此，如果一个对等体想要加入 *Org1MSP* 内部的网络，它的证书的根证书应该在这个 *MSPDir* 的 *cacerts* 文件夹中，它的 TLS 证书应该由*TLS certs 中的证书签名。*

![](img/1ab8da14e3a3cd88e50d2549422e7144.png)

Org1 section inside configtx.yaml

# 步骤 1(创建项目文件夹)

创建一个名为 tutorial 的文件夹，并将其作为您的工作目录。

```
$ mkdir tutorial
$ cd tutorial
```

# 步骤 2(设置结构 CA 服务器)

CA (certificate authority)是管理证书生命周期的权威机构，它是颁发证书和撤销证书的 CA。

我们将使用结构 CA 生成所有订购者和同行的证书，它仅由 hyperledger 结构提供，我们将推出 2 个结构 CA 服务器，一个用于 CA，一个用于 TLSCA。
我们将使用 docker 启动服务器，CA 服务器将在端口 7054 上运行，TLSCA 将在端口 8054 上运行。

```
$ nano docker-compose-ca.yaml
```

将以下文本复制到该文件中。

```
version: '2'
networks:
  fabric:
services:
  ca.vaibhav.com:
    image: hyperledger/fabric-ca:1.4
    hostname: ca.vaibhav.com
    environment:
      - FABRIC_CA_HOME=/etc/hyperledger/fabric-ca-server
      - FABRIC_CA_SERVER_CA_NAME=rootca
      - FABRIC_CA_SERVER_TLS_ENABLED=false
      - FABRIC_CA_SERVER_TLS_CERTFILE=/etc/hyperledger/fabric-ca-server-config/server.crt
      - FABRIC_CA_SERVER_TLS_KEYFILE=/etc/hyperledger/fabric-ca-server-config/server.key
      - FABRIC_CA_SERVER_PORT=7054
    ports:
      - "7054:7054"
    command: sh -c 'fabric-ca-server start --ca.certfile /etc/hyperledger/fabric-ca-server-config/server.crt --ca.keyfile /etc/hyperledger/fabric-ca-server-config/server.key -b admin:adminpw -d'
    volumes:
      - ./ca:/etc/hyperledger/fabric-ca-server-config
    networks:
      fabric:
        aliases:
          - ca.vaibhav.com
  tlsca.vaibhav.com:
    image: hyperledger/fabric-ca:1.4
    hostname: tlsca.vaibhav.com
    environment:
      - FABRIC_CA_HOME=/etc/hyperledger/fabric-ca-server
      - FABRIC_CA_SERVER_CA_NAME=roottlsca
      - FABRIC_CA_SERVER_TLS_ENABLED=false
      - FABRIC_CA_SERVER_TLS_CERTFILE=/etc/hyperledger/fabric-ca-server-config/server.crt
      - FABRIC_CA_SERVER_TLS_KEYFILE=/etc/hyperledger/fabric-ca-server-config/server.key
      - FABRIC_CA_SERVER_PORT=8054
    ports:
      - "8054:8054"
    command: sh -c 'fabric-ca-server start --ca.certfile /etc/hyperledger/fabric-ca-server-config/server.crt --ca.keyfile /etc/hyperledger/fabric-ca-server-config/server.key -b admin:adminpw -d'
    volumes:
      - ./tlsca:/etc/hyperledger/fabric-ca-server-config
    networks:
      fabric:
        aliases:
          - tlsca.vaibhav.com
```

使用 docker-compose 启动服务器。

```
$ docker-compose -f docker-compose-ca.yaml up -d
```

此后，两个 ca 服务器的容器都将启动并运行。
使用以下命令检查它们是否正在运行，并且仅当您看到两个容器都已启动并运行时才继续

```
$ docker ps
```

# 步骤 3(为订购方和对等方生成证书)

现在，我们将使用生成的 ca 为订购者和对等方生成 MSP 和 TLS 证书。

根据结构[文档](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html#msp-setup-on-the-peer-orderer-side)，以下是订购方和对等方的 msp 结构

![](img/eda166b56695f8c9ad423a8fb0dfe96a.png)

msp folder structure for orderers and peers

**四个必选文件夹分别是:-**
1。 *admincerts* :-我们需要手动创建这个文件夹并将管理员的 CA 证书放入其中，我们将生成管理员证书并放入其中。
2。 *cacerts* :-由 fabric CA 在生成证书时自动创建。
3。 *signcerts* :-由 fabric CA 在生成证书时自动创建。
4。*密钥库* :-由 fabric CA 在生成证书时自动创建。

首先，我们将创建 *org0* 和 *org1* MSP 结构。

```
$ mkdir -p {orderers/org0/{msp/{cacerts,tlscacerts,admincerts},orderer1,orderer2,orderer3},peers/org1/{msp/{cacerts,tlscacerts,admincerts},peer1},rootca-admin,roottlsca-admin}
```

我们现在将生成 CA 服务器的管理证书，该证书将用于在 CA 服务器上注册对等方和订购方，在注册之后，可以为它们生成证书。

```
$ export FABRIC_CA_HOME=./rootca-admin
$ fabric-ca-client enroll -u [http://admin:adminpw@localhost:7054](http://admin:adminpw@localhost:7054)
```

**生成 MSP 证书**

我们将在每个组织中生成订购者和同行证书以及 1 个管理员证书。

```
$ fabric-ca-client register --id.name orderer1.org0.com --id.secret secret --id.type orderer -u [http://localhost:7054](http://localhost:7054C)$ fabric-ca-client register --id.name orderer2.org0.com --id.secret secret --id.type orderer -u [http://localhost:7054](http://localhost:7054C)$ fabric-ca-client register --id.name orderer3.org0.com --id.secret secret --id.type orderer -u [http://localhost:7054](http://localhost:7054C)$ fabric-ca-client register --id.name peer1.org1.com --id.secret secret --id.type peer -u [http://localhost:7054](http://localhost:7054)$ fabric-ca-client register --id.name admin.org0.com --id.secret secret --id.type admin -u [http://localhost:7054](http://localhost:7054)$ fabric-ca-client register --id.name admin.org1.com --id.secret secret --id.type admin -u [http://localhost:7054](http://localhost:7054)$ export FABRIC_CA_HOME=./orderers/org0/orderer1/
$ fabric-ca-client enroll --csr.hosts "orderer1.org0.com" -u [http://orderer1.org0.com:secret@localhost:7054](http://admin:adminpw@localhost:7054)$ export FABRIC_CA_HOME=./orderers/org0/orderer2/
$ fabric-ca-client enroll --csr.hosts "orderer2.org0.com" -u [http://orderer2.org0.com:secret@localhost:7054](http://admin:adminpw@localhost:7054)$ export FABRIC_CA_HOME=./orderers/org0/orderer3/
$ fabric-ca-client enroll --csr.hosts "orderer3.org0.com" -u [http://orderer3.org0.com:secret@localhost:7054](http://admin:adminpw@localhost:7054)$ export FABRIC_CA_HOME=./peers/org1/peer1/
$ fabric-ca-client enroll --csr.hosts "peer1.org1.com" -u [http://peer1.org1.com:secret@localhost:7054](http://peer1.org1.com:secret@localhost:7054)$ export FABRIC_CA_HOME=./orderers/org0/orderer1/msp/user/
$ fabric-ca-client enroll --csr.hosts "admin.org0.com" -u [http://admin.org0.com:secret@localhost:7054](http://admin.org0.com:secret@localhost:7054) -M admin$ export FABRIC_CA_HOME=./peers/org1/peer1/msp/user/
$ fabric-ca-client enroll --csr.hosts "admin.org1.com" -u [http://admin.org1.com:secret@localhost:7054](http://admin.org0.com:secret@localhost:7054) -M admin
```

**生成 TLS 证书**

这将与生成 MSP 证书相同，但我们将使用 TLS-CA 服务器而不是 CA 服务器，我们不会生成管理员的 TLS 证书，因为我们不会使用 mutual-tls 将客户端连接到网络。

```
$ export FABRIC_CA_HOME=./roottlsca-admin
$ fabric-ca-client enroll -u [http://admin:adminpw@localhost:](http://admin:adminpw@localhost:7054)8054$ fabric-ca-client register --id.name orderer1.org0.com --id.secret secret --id.type orderer -u [http://localhost:8054](http://localhost:8054)$ fabric-ca-client register --id.name orderer2.org0.com --id.secret secret --id.type orderer -u [http://localhost:8054](http://localhost:8054)$ fabric-ca-client register --id.name orderer3.org0.com --id.secret secret --id.type orderer -u [http://localhost:8054](http://localhost:8054)$ fabric-ca-client register --id.name peer1.org1.com --id.secret secret --id.type peer -u [http://localhost:8054](http://localhost:8054)$ export FABRIC_CA_HOME=./orderers/org0/orderer1/
$ fabric-ca-client enroll --csr.hosts "orderer1.org0.com" -u [http://orderer1.org0.com:secret@localhost:8054](http://orderer1.org0.com:secret@localhost:8054) -M tls$ export FABRIC_CA_HOME=./orderers/org0/orderer2/
$ fabric-ca-client enroll --csr.hosts "orderer2.org0.com" -u [http://orderer2.org0.com:secret@localhost:8054](http://orderer2.org0.com:secret@localhost:8054) -M tls$ export FABRIC_CA_HOME=./orderers/org0/orderer3/
$ fabric-ca-client enroll --csr.hosts "orderer3.org0.com" -u [http://orderer3.org0.com:secret@localhost:8054](http://orderer3.org0.com:secret@localhost:8054) -M tls$ export FABRIC_CA_HOME=./peers/org1/peer1/
$ fabric-ca-client enroll --csr.hosts "peer1.org1.com" -u [http://peer1.org1.com:secret@localhost:8054](http://peer1.org1.com:secret@localhost:8054) -M tls
```

我们还将更改 tls 私钥的名称，因为 CA 会为此生成一个随机名称，并且我们需要在稍后创建 *docker-compose.yaml* 时引用它的路径。所以，我们把名字改成“server.key”。

```
$ mv orderers/org0/orderer1/tls/keystore/* orderers/org0/orderer1/tls/keystore/server.key$ mv orderers/org0/orderer2/tls/keystore/* orderers/org0/orderer2/tls/keystore/server.key$ mv orderers/org0/orderer3/tls/keystore/* orderers/org0/orderer3/tls/keystore/server.key$ mv peers/org1/peer1/tls/keystore/* peers/org1/peer1/tls/keystore/server.key
```

# 步骤 4(为 org0 和 org1 创建 MSP 文件夹)

我已经解释了什么是 MSP 以及它为什么重要。你可以在这里了解更多关于 MSP [的信息。](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html#msp-setup-on-the-peer-orderer-side)

在我们的网络中，我们有两个组织“org0”和“org1”。
为两个组织生成 *msp* 文件夹。

```
$ cp ca/server.crt orderers/org0/msp/cacerts$ cp ca/server.crt peers/org1/msp/cacerts$ cp tlsca/server.crt orderers/org0/msp/tlscacerts$ cp tlsca/server.crt peers/org1/msp/tlscacerts$ cp orderers/org0/orderer1/msp/user/admin/signcerts/cert.pem orderers/org0/msp/admincerts/
$ cp peers/org1/peer1/msp/user/admin/signcerts/cert.pem peers/org1/msp/admincerts/$ mkdir orderers/org0/orderer1/msp/admincerts orderers/org0/orderer2/msp/admincerts orderers/org0/orderer3/msp/admincerts peers/org1/peer1/msp/admincerts$ cp orderers/org0/orderer1/msp/user/admin/signcerts/cert.pem orderers/org0/orderer1/msp/admincerts$ cp orderers/org0/orderer1/msp/user/admin/signcerts/cert.pem orderers/org0/orderer2/msp/admincerts$ cp orderers/org0/orderer1/msp/user/admin/signcerts/cert.pem orderers/org0/orderer3/msp/admincerts$ cp peers/org1/peer1/msp/user/admin/signcerts/cert.pem peers/org1/peer1/msp/admincerts$ mkdir peers/org1/peer1/msp/user/admin/admincerts
$ cp peers/org1/peer1/msp/user/admin/signcerts/cert.pem peers/org1/peer1/msp/user/admin/admincerts/
```

# 步骤 5(生成起源块和通道)

为了启动订购者，包含网络信息的起源块是重要的，它包含关于它识别的 MSP 的信息，作为这些 MSP 的一部分的组织将能够参与网络。我们将提供在上一步中刚刚创建的 msp 文件夹来生成 genesis 块。
还将创建一个样本通道，只是为了加入它并检查整个网络是否正常工作。
创建新文件:-

```
$ nano configtx.yaml
```

将以下内容粘贴到该文件中:-

```
Organizations:
    - &org0
        Name: org0
# ID to load the MSP definition as
        ID: org0
# MSPDir is the filesystem path which contains the MSP configuration
        MSPDir: orderers/org0/msp
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('org0.member')"
            Writers:
                Type: Signature
                Rule: "OR('org0.member')"
            Admins:
                Type: Signature
                Rule: "OR('org0.admin')"
    - &org1
            # DefaultOrg defines the organization which is used in the sampleconfig
            # of the fabric.git development environment
            Name: org1
    # ID to load the MSP definition as
            ID: org1
            MSPDir: peers/org1/msp
            Policies:
                Readers:
                    Type: Signature
                    Rule: "OR('org1.admin', 'org1.peer', 'org1.client')"
                Writers:
                    Type: Signature
                    Rule: "OR('org1.admin', 'org1.client')"
                Admins:
                    Type: Signature
                    Rule: "OR('org1.admin')"
Capabilities:
    Channel: &ChannelCapabilities
        V1_4_2: true
# Orderer capabilities apply only to the orderers, and may be safely
    # used with prior release peers.
    # Set the value of the capability to true to require it.
    Orderer: &OrdererCapabilities
        V1_4_2: true
# Application capabilities apply only to the peer network, and may be safely
    # used with prior release orderers.
    # Set the value of the capability to true to require it.
    Application: &ApplicationCapabilities
        V1_4_2: true
Application: &ApplicationDefaults
# Organizations is the list of orgs which are defined as participants on
    # the application side of the network
    Organizations:
    Policies:
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"
        LifecycleEndorsement:
            Type: ImplicitMeta
            Rule: "MAJORITY Endorsement"
        Endorsement:
            Type: ImplicitMeta
            Rule: "MAJORITY Endorsement"
    Capabilities:
        <<: *ApplicationCapabilities
Orderer: &OrdererDefaults
# Orderer Type: The orderer implementation to start
    OrdererType: etcdraft
    EtcdRaft:
        Consenters:
        - Host: orderer1.org0.com
          Port: 7050
          ClientTLSCert: orderers/org0/orderer1/tls/signcerts/cert.pem
          ServerTLSCert: orderers/org0/orderer1/tls/signcerts/cert.pem
        - Host: orderer2.org0.com
          Port: 7050
          ClientTLSCert: orderers/org0/orderer2/tls/signcerts/cert.pem
          ServerTLSCert: orderers/org0/orderer2/tls/signcerts/cert.pem
        - Host: orderer3.org0.com
          Port: 7050
          ClientTLSCert: orderers/org0/orderer3/tls/signcerts/cert.pem
          ServerTLSCert: orderers/org0/orderer3/tls/signcerts/cert.pem
# Batch Timeout: The amount of time to wait before creating a batch
    BatchTimeout: 2s
# Batch Size: Controls the number of messages batched into a block
    BatchSize:
# Max Message Count: The maximum number of messages to permit in a batch
        MaxMessageCount: 10
# Absolute Max Bytes: The absolute maximum number of bytes allowed for
        # the serialized messages in a batch.
        AbsoluteMaxBytes: 99 MB
# Preferred Max Bytes: The preferred maximum number of bytes allowed for
        # the serialized messages in a batch. A message larger than the preferred
        # max bytes will result in a batch larger than preferred max bytes.
        PreferredMaxBytes: 512 KB
# Organizations is the list of orgs which are defined as participants on
    # the orderer side of the network
    Organizations:
# Policies defines the set of policies at this level of the config tree
    # For Orderer policies, their canonical path is
    #   /Channel/Orderer/<PolicyName>
    Policies:
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"
        # BlockValidation specifies what signatures must be included in the block
        # from the orderer for the peer to validate it.
        BlockValidation:
            Type: ImplicitMeta
            Rule: "ANY Writers"
Channel: &ChannelDefaults
    # Policies defines the set of policies at this level of the config tree
    # For Channel policies, their canonical path is
    #   /Channel/<PolicyName>
    Policies:
        # Who may invoke the 'Deliver' API
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        # Who may invoke the 'Broadcast' API
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        # By default, who may modify elements at this config level
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"
# Capabilities describes the channel level capabilities, see the
    # dedicated Capabilities section elsewhere in this file for a full
    # description
    Capabilities:
        <<: *ChannelCapabilities
################################################################################
#
#   Profile
#
#   - Different configuration profiles may be encoded here to be specified
#   as parameters to the configtxgen tool
#
################################################################################
Profiles:
    default:
        Consortium: SampleConsortium
        <<: *ChannelDefaults
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *org1
            Capabilities:
                <<: *ApplicationCapabilities
    genesis:
        <<: *ChannelDefaults
        Capabilities:
            <<: *ChannelCapabilities
        Orderer:
            <<: *OrdererDefaults
            OrdererType: etcdraft
            Addresses:
                - orderer1.org0.com:7050
                - orderer2.org0.com:7050
                - orderer3.org0.com:7050
            Organizations:
                - *org0
            Capabilities:
                <<: *OrdererCapabilities
        Application:
            <<: *ApplicationDefaults
            Organizations:
            - <<: *org0
        Consortiums:
            SampleConsortium:
                Organizations:
                - *org1
```

使用 *configtxgen* 二进制文件生成 genesis 块和默认通道。

```
$ mkdir channel-artifacts$ configtxgen -profile genesis -outputBlock channel-artifacts/genesis.block$ configtxgen -profile default -outputCreateChannelTx channel-artifacts/default.tx -channelID default
```

现在我们有了创建通道的 genesis 块和事务，我们可以继续打开我们的网络。

# 步骤 6(启动网络)

首先，我们将创建一个 docker-compose.yaml 文件。

```
$ nano docker-compose.yaml
```

将下面的内容复制粘贴到这个文件中。

```
version: '2'
volumes:
  orderer1.org0.com:
  orderer2.org0.com:
  orderer3.org0.com:
  peer1.org1.com:
networks:
  fabric:
services:
  orderer1.org0.com:
    hostname: orderer1.org0.com
    image: hyperledger/fabric-orderer:1.4.4
    environment:
      - FABRIC_LOGGING_SPEC=DEBUG
      - ORDERER_GENERAL_LISTENADDRESS=0.0.0.0
      - ORDERER_GENERAL_GENESISMETHOD=file
      - ORDERER_GENERAL_GENESISFILE=/var/hyperledger/orderer/orderer.genesis.block
      - ORDERER_GENERAL_LOCALMSPDIR=/var/hyperledger/orderer/msp
      - ORDERER_GENERAL_LOCALMSPID=org0
      - ORDERER_GENERAL_TLS_ENABLED=true
      - ORDERER_GENERAL_TLS_PRIVATEKEY=/var/hyperledger/orderer/tls/keystore/server.key
      - ORDERER_GENERAL_TLS_CERTIFICATE=/var/hyperledger/orderer/tls/signcerts/cert.pem
      - ORDERER_GENERAL_TLS_ROOTCAS=[/var/hyperledger/orderer/tls/cacerts/localhost-8054.pem]
      - ORDERER_GENERAL_CLUSTER_CLIENTCERTIFICATE=/var/hyperledger/orderer/tls/signcerts/cert.pem
      - ORDERER_GENERAL_CLUSTER_CLIENTPRIVATEKEY=/var/hyperledger/orderer/tls/keystore/server.key
      - ORDERER_GENERAL_CLUSTER_ROOTCAS=[/var/hyperledger/orderer/tls/cacerts/localhost-8054.pem]
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric
    command: orderer
    volumes:
        - ./channel-artifacts/genesis.block:/var/hyperledger/orderer/orderer.genesis.block
        - ./orderers/org0/orderer1/msp:/var/hyperledger/orderer/msp
        - ./orderers/org0/orderer1/tls/:/var/hyperledger/orderer/tls
        - orderer1.org0.com:/var/hyperledger/production/orderer
    networks:
      fabric:
        aliases:
          - orderer1.org0.com
  orderer2.org0.com:
    hostname: orderer2.org0.com
    image: hyperledger/fabric-orderer:1.4.4
    environment:
      - FABRIC_LOGGING_SPEC=DEBUG
      - ORDERER_GENERAL_LISTENADDRESS=0.0.0.0
      - ORDERER_GENERAL_GENESISMETHOD=file
      - ORDERER_GENERAL_GENESISFILE=/var/hyperledger/orderer/orderer.genesis.block
      - ORDERER_GENERAL_LOCALMSPID=org0
      - ORDERER_GENERAL_LOCALMSPDIR=/var/hyperledger/orderer/msp
      - ORDERER_GENERAL_TLS_ENABLED=true
      - ORDERER_GENERAL_TLS_PRIVATEKEY=/var/hyperledger/orderer/tls/keystore/server.key
      - ORDERER_GENERAL_TLS_CERTIFICATE=/var/hyperledger/orderer/tls/signcerts/cert.pem
      - ORDERER_GENERAL_TLS_ROOTCAS=[/var/hyperledger/orderer/tls/cacerts/localhost-8054.pem]
      - ORDERER_GENERAL_CLUSTER_CLIENTCERTIFICATE=/var/hyperledger/orderer/tls/signcerts/cert.pem
      - ORDERER_GENERAL_CLUSTER_CLIENTPRIVATEKEY=/var/hyperledger/orderer/tls/keystore/server.key
      - ORDERER_GENERAL_CLUSTER_ROOTCAS=[/var/hyperledger/orderer/tls/cacerts/localhost-8054.pem]
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric
    command: orderer
    volumes:
        - ./channel-artifacts/genesis.block:/var/hyperledger/orderer/orderer.genesis.block
        - ./orderers/org0/orderer2/msp:/var/hyperledger/orderer/msp
        - ./orderers/org0/orderer2/tls/:/var/hyperledger/orderer/tls
        - orderer2.org0.com:/var/hyperledger/production/orderer
    networks:
      fabric:
        aliases:
          - orderer2.org0.com
  orderer3.org0.com:
    hostname: orderer3.org0.com
    image: hyperledger/fabric-orderer:1.4.4
    environment:
      - FABRIC_LOGGING_SPEC=DEBUG
      - ORDERER_GENERAL_LISTENADDRESS=0.0.0.0
      - ORDERER_GENERAL_GENESISMETHOD=file
      - ORDERER_GENERAL_GENESISFILE=/var/hyperledger/orderer/orderer.genesis.block
      - ORDERER_GENERAL_LOCALMSPID=org0
      - ORDERER_GENERAL_LOCALMSPDIR=/var/hyperledger/orderer/msp
      - ORDERER_GENERAL_TLS_ENABLED=true
      - ORDERER_GENERAL_TLS_PRIVATEKEY=/var/hyperledger/orderer/tls/keystore/server.key
      - ORDERER_GENERAL_TLS_CERTIFICATE=/var/hyperledger/orderer/tls/signcerts/cert.pem
      - ORDERER_GENERAL_TLS_ROOTCAS=[/var/hyperledger/orderer/tls/cacerts/localhost-8054.pem]
      - ORDERER_GENERAL_CLUSTER_CLIENTCERTIFICATE=/var/hyperledger/orderer/tls/signcerts/cert.pem
      - ORDERER_GENERAL_CLUSTER_CLIENTPRIVATEKEY=/var/hyperledger/orderer/tls/keystore/server.key
      - ORDERER_GENERAL_CLUSTER_ROOTCAS=[/var/hyperledger/orderer/tls/cacerts/localhost-8054.pem]
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric
    command: orderer
    volumes:
        - ./channel-artifacts/genesis.block:/var/hyperledger/orderer/orderer.genesis.block
        - ./orderers/org0/orderer3/msp:/var/hyperledger/orderer/msp
        - ./orderers/org0/orderer3/tls/:/var/hyperledger/orderer/tls
        - orderer3.org0.com:/var/hyperledger/production/orderer
    networks:
      fabric:
        aliases:
          - orderer3.org0.com
  peer1.org1.com:
    container_name: peer1.org1.com
    hostname: peer1.org1.com
    image: hyperledger/fabric-peer:1.4.4
    environment:
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - FABRIC_LOGGING_SPEC=DEBUG
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_GOSSIP_USELEADERELECTION=false
      - CORE_PEER_GOSSIP_ORGLEADER=true
      - CORE_PEER_PROFILE_ENABLED=true
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/signcerts/cert.pem
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/keystore/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/cacerts/localhost-8054.pem
      # Allow more time for chaincode container to build on install.
      - CORE_CHAINCODE_EXECUTETIMEOUT=300s
      - CORE_PEER_ID=peer1.org1.com
      - CORE_PEER_ADDRESS=peer1.org1.com:7051
      - CORE_PEER_LISTENADDRESS=0.0.0.0:7051
      - CORE_PEER_CHAINCODEADDRESS=peer1.org1.com:7052
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:7052
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer1.org1.com:7051
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer1.org1.com:7051
      - CORE_PEER_LOCALMSPID=org1
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: peer node start
    volumes:
        - /var/run/:/host/var/run/
        - ./peers/org1/peer1/msp:/etc/hyperledger/fabric/msp
        - ./peers/org1/peer1/tls:/etc/hyperledger/fabric/tls
        - peer1.org1.com:/var/hyperledger/production
    networks:
      fabric:
        aliases:
          - peer1.org1.com

  cli:
    container_name: cli
    image: hyperledger/fabric-tools:1.4.4
    tty: true
    stdin_open: true
    environment:
      - GOPATH=/opt/gopath
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      #- FABRIC_LOGGING_SPEC=DEBUG
      - FABRIC_LOGGING_SPEC=INFO
      - CORE_PEER_ID=cli
      - CORE_PEER_ADDRESS=peer1.org1.com:7051
      - CORE_PEER_LOCALMSPID=org1
      - CORE_PEER_TLS_ENABLED=true
#      - CORE_PEER_TLS_CERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/server.crt
#      - CORE_PEER_TLS_KEY_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/org1/msp/tlscacerts/server.crt
      - CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/org1/peer1/msp/user/admin
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: /bin/bash
    volumes:
        - /var/run/:/host/var/run/
        - ./peers/org1/:/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/org1/
        - ./channel-artifacts:/opt/gopath/src/github.com/hyperledger/fabric/peer/channel-artifacts
    networks:
      fabric:
```

调出网络

```
$ docker-compose up -d
```

检查是否所有容器都已启动并运行

```
$ docker ps
```

如果你看到容器在运行，祝贺我们的网络运行成功。

# 步骤 7(创建和加入渠道)

现在，让我们创建一个通道，并加入我们唯一的同行。首先，让我们来看看我们的 cli 容器

```
$ docker exec -it cli bash
```

在容器内部，运行以下命令分别创建和加入对等通道。

```
$ peer channel create -f channel-artifacts/default.tx -c default -o orderer1.org0.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/org1/msp/tlscacerts/server.crt$ peer channel join -b default.block -o orderer1.org0.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/org1/msp/tlscacerts/server.crt
```

检查对等方是否加入了通道

```
$ peer channel list
```

您应该看到一个名为 *default 的通道。*

感谢您学习本教程，您刚刚自己创建了一个简单的结构网络。:)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com?utm_source=coinmonks)

[![](img/160ce73bd06d46c2250251e7d5969f9d.png)](https://coincodecap.com?utm_source=coinmonks)