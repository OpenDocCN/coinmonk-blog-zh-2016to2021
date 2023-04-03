# hyperledger composer—Docker Swarm

> 原文：<https://medium.com/coinmonks/hyperledger-composer-docker-swarm-a15bef803c8b?source=collection_archive---------0----------------------->

![](img/ae5bbc39965187645d0917c1eaf056dd.png)

本文基于基本的 hyperledger fabric-tools 示例。

```
curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.zipunzip fabric-dev-servers.zip
```

目标是以 fabric-tools 为例，尝试使用 docker swarm 在多个节点上部署一个多对等解决方案。这些是我的发现。

# 不断增加的资源

第一步是将加密资源挂载为 NFS，这样它们就可以在整个网络中共享。我将导出文件设置如下:

```
/srv/NFS *(rw,sync,no_subtree_check,no_root_squash,no_all_squash)
```

在文件夹`srv/NFS`中，我建立了`cryptoconfig.yaml`文件和`configtx.yaml`文件。从那里，我使用 hyperledger 工具设置资源。要将 NFS 卷安装到 docker 容器，需要将 docker-compose 从`version ‘2'`更改为`version ‘3.2'`

下面是将驱动器安装到对等机的 composer 文件的设置示例:

```
 peer0:
    image: hyperledger/fabric-peer:x86_64-1.0.4
    environment:
      - CORE_LOGGING_PEER=debug
      - CORE_CHAINCODE_LOGGING_LEVEL=DEBUG
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - CORE_PEER_ID=peer0
      - CORE_PEER_ADDRESS=peer_peer0:7051    
      - CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=net
      - CORE_PEER_LOCALMSPID=MarsMSP
      - CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/peer/msp
      - CORE_LEDGER_STATE_STATEDATABASE=CouchDB
      - CORE_LEDGER_STATE_COUCHDBCONFIG_COUCHDBADDRESS=couch_couchdb0:5984
      - CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/msp/users/Admin@example.com/msp
    working_dir: /etc/hyperledger/configtx
    command: peer node start --peer-defaultchain=false
    ports:
      - 7051:7051
      - 7053:7053
    volumes:
        - /var/run/:/host/var/run/
        - /var/run/docker.sock:/var/run/docker.sock
        - type: volume
          source: tx
          target: /etc/hyperledger/configtx
          volume:
            nocopy: true
        - type: volume
          source: peer0MSP
          target: /etc/hyperledger/peer/msp
          volume:
            nocopy: true

volumes: 
  tx:
    driver_opts:
      type: "nfs"
      o: "addr=XXX.XXX.XXX.XXX,nolock,soft,rw"
      device: ":/srv/NFS"  

  peer0MSP:
    driver_opts:
      type: "nfs"
      o: "addr=XXX.XXX.XXX.XXX,nolock,soft,rw"
      device: ":/srv/NFS/crypto-config/peerOrganizations/or1.example.com/peers/peer0.example.com/msp"
```

# 群体配置

对于这个测试，我使用了三个节点。使用 docker swarm 文档，我将节点连接在一起。然后，在创建不同的堆栈之前，我创建了一个覆盖网络。

```
docker network create -d overlay --attachable net
```

在 docker 为每个堆栈编写 yml 文件时，覆盖网络被指定为外部网络。

```
networks:
  net:
    external:
      name: net
```

Docker stack deploy 不像 docker compose 那样允许选项`depends_on`，因此我为每个需要的组件创建了单独的 compose 文件。然后，我使用一个 bash 脚本文件来调用每个单独的堆栈。

```
docker network create -d overlay --attachable netdocker stack deploy -c docker-compose.couch.yml couch 
sleep 5
docker stack deploy -c docker-compose.otx.yml otx
sleep 5
docker stack deploy -c docker-compose.peers.yml peer
docker stack deploy -c docker-compose.deploy.yml deploy
```

非标准的主要内容是在部署映像中。这是从一个`hyperledger/fabric-tools`图像修改而来的，添加了 composer 开发工具。该图像然后共享来自 NFS 卷的基本作曲项目。使用这个映像，我可以将 composer 应用程序部署到 swarm。当我在 swarm 中使用一个容器来部署我的 composer 应用程序时，我直接引用了容器，如下所示:

```
localhost:7051 --> peer_peer0:7051
localhost:7083 --> peer_peer1:7051
```

在 composer 项目中更改`connection.json`文件并直接引用容器，这意味着我可以登录到部署容器并部署业务网络档案。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)