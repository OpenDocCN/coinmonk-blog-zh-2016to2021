# 区块链 Hyperledger 结构—错误和解决方案

> 原文：<https://medium.com/coinmonks/hyperledger-fabric-composer-errors-solutions-827112a3fce6?source=collection_archive---------2----------------------->

![](img/6b0e9686e9a9fa1ad47fc3857bd8edfa.png)

Source: Google(iStock)

***阅读更多来自——***[](https://blockchaindreamlab.com/)****——****

*你好，在这篇文章中，我想分享一些你可能面临的错误和可能的解决方案。*

*请注意，当我在区块链项目中工作时，我已经面临了“n”个错误，我花了无数个小时来阅读文章，联系专家来修复这些错误。这篇文章只是为了帮助初学者&努力解决在开发阶段经常出现的错误。如果您有任何错误，这里没有列出，请随时评论，将得到它补充。*

# *错误 1:*

****错误:尝试 ping 时出错。错误:2 未知:请确保链码测试注册表已成功实例化，然后重试:找不到名为“测试注册表”的链码****

***解决方案:**确保在开始之前已经安装了网络。使用以下命令启动网络*

> *- composer 网络安装-card peer admin @ hlf v1-archive file network-name @ 0 . 0 . 1 . bna*
> 
> *- composer 网络开始—网络名称网络名称—网络版本 0.0.1 —网络管理员管理员—网络管理员注册密码管理员 pw —卡 PeerAdmin@hlfv1 —文件网络管理员.卡*

# *错误 2:*

*错误:
docker:来自守护进程的错误响应:冲突。容器名称“/composer-playground”已被容器“9 f 224 b 04663 a4 fa 65 f 50843 e 5 f 7 e 28 f 7 e 7 df 662507 a9 b 42 a 5 DFC 72 FD 3433405”使用。您必须删除(或重命名)该容器才能重用该名称。
参见“docker 运行-帮助”。*

***解决方案:**通过运行以下命令杀死 docker&重新执行 composer-playground 命令(" docker run—name composer-playground—publish 8080:8080 hyperledger/composer-playground ")*

> *档案员 rm -f $(档案员 ps -a -q)*
> 
> *docker kill $(docker ps -q)*
> 
> *docker rm $(docker ps -aq)*
> 
> *docker rmi $(docker 图像 dq-* -q)*

# ***错误号 3:***

***错误:链码已经存在错误***

*解决方案:删除 docker 图像，停止结构并重新开始结构。一旦它运行良好，然后再次开始部署业务网卡。*

> *。/stopFabric.sh*
> 
> *。/startFabric.sh*

# *错误 4:*

*错误:NG 服务错误*

*解决方案:用 npm 代替 ng serve。*

> *npm 开始*

# ***错误号 5:***

*错误:授权错误 REST-API*

***解决方案:**在环境变量中设置 COMPOSER_PROVIDERS，重启 composer-rest-server*

> *点击此链接[https://github . com/hyperledger/composer/blob/master/packages/composer-website/jekyldocs/integrating/enabling-rest-authentic ation . MD](https://github.com/hyperledger/composer/blob/master/packages/composer-website/jekylldocs/integrating/enabling-rest-authentication.md)*

# ***错误 6:***

*从业务网络定义中发现类型…
连接失败:错误:尝试 ping 时出错。错误:2 未知:请确保链码“网络名称”已成功实例化，然后重试:找不到名为“网络名称”的链码*

*解决方案:确保在运行 composer-rest-server 命令之前安装并启动业务网络。*

> *1)composer 网络安装-card peer admin @ hlf v1-archive file network-name @ 0 . 0 . 1 . bna*
> 
> *2)composer network start—network name ' network-name '—network version 0 . 0 . 1—network admin admin—networkadminenroll secret adminpw—card peer admin @ hlf v1—file network admin . card*

# ***错误号 7:***

*错误:for couchdb 无法为服务 couchdb 创建容器:冲突。容器名称“/couchdb”已被容器“de 8 ce af 8 f 21 b 53 bb 4c 9883 EB 618448 f 6 f 07 c 7690 bea 8 ab 122 ef 3 b 689 e 846 ea B4”使用。你必须删除(或重命名)创建 orderer.example.com…错误*

*解决方案:通过运行以下命令杀死停靠者*

> *文档 rm -f $(文档 ps -aq)*

# ***错误号:8***

*错误:找不到模块“fabric-client”*

*解决方案:执行 npm 更新，这将安装必要的插件和模块。*

> *npm 更新*

# *错误号:9*

*错误:npm 错误！这可能不是 npm 的问题。上面可能有额外的日志输出。npm 警告本地 package.json 存在，但 node_modules 缺失，是否要安装？*

*解决方案:执行 npm 安装，这将安装必要的节点模块。*

> *npm 安装*

# *错误号:10*

*错误:由“yo fabric-composer”生成的 Angular2 应用程序没有用于交易的用户界面*

*解决方案:我们在 Github 中有一个未解决的问题——请关注该问题以获取更多更新*

## ***解决方案更新:***

*转到 src/app/ <transaction>文件夹中的 <transaction>.component.html 文件，检查行
<button type = " button " class = " BTN BTN-primary invokeTransactionBtn " data-toggle = " modal ">Invoke</button></transaction></transaction>*

*只需给它添加数据目标属性如下:
<button type = " button " class = " BTN BTN-primary invokeTransactionBtn " data-toggle = " modal " data-target = " # addTransactionModal ">Invoke</button>*

*#addTransactionModal 是模式的 id，其形式为。*

> *链接:[https://github.com/hyperledger/composer/issues/4121](https://github.com/hyperledger/composer/issues/4121)*

# *错误号:11*

*错误:连接失败:错误:尝试 ping 时出错。错误:Composer 运行时(0.19.8)与客户端(0.19.5)不兼容
将在下一次请求时重试。*

*解决方案:*

> *。/stopFabric.sh*
> 
> *`npm install -g composer-playground@0.19.8`*
> 
> *作曲-休息-服务器*

# *错误 12:*

*错误:curl: (7)无法连接到本地主机端口 49160:连接被拒绝*

*解决方案:docker run-RM-I-t-p 49160:8080<your username="">/node-app</your>*

*注意:这种错误只会在您构建自己的 docker 容器并试图执行它时出现。*

# *错误号 13:*

*错误:network composer _ default id b 1067 bde C4 ca 1800510 fc 9 cc 1 f 5d 3500736 e 48920693 b 03831 df 0 D8 d 8472 ffac 具有活动端点*

*你可能会遇到这个错误，当你试图执行新的结构服务器(但旧的仍在运行状态)*

*解决方案:使用. stopFabric.sh 或. stop.sh(无论您的网络中有什么脚本)停止已经运行的 Fabric 服务器，然后应用以下命令删除正在运行的 docker 容器，并重新执行 fabric*

> *档案员 rm -f $(档案员 ps -a -q)*

# *错误号 14:*

*错误:[节点。JS 错误:ENOSPC](https://stackoverflow.com/questions/22475849/node-js-error-enospc)*

*解决方案:[https://stack overflow . com/questions/22475849/node-js-error-Enos PC](https://stackoverflow.com/questions/22475849/node-js-error-enospc)。*

# ***第 15 号错误**:*

*错误:无法创建交付客户端:订购者客户端无法连接到 order . example . com:7050:无法创建新连接:超过了上下文截止时间*

***解决方案:**您需要首先查看订购者日志，以确保幕后发生的事情。*

****第一步:docker 日志 orderer.example.com****

*步骤 2:转到 configtx.yaml 文件并检查功能*

*确保您下载了正确的版本*

# *错误号 16:*

> *错误:无法导入业务网卡*
> 
> *未能导入业务网卡
> 关键字:必需的
> 数据路径:。peers[' peer 0 . org 1 . example . com ']
> schema path:#/required
> params:
> missing property:eventUrl
> 消息:应具有必需的属性“event URL”*
> 
> *错误:在卡
> 命令的连接配置文件中发现错误失败*

*解决方案:您正在尝试运行 fabric v1.1，但它拉出 fabric v1.2 createpeeradmin.sh*

# ***错误号 17***

*错误:容器被标记为删除，无法启动*

*有时，当你杀死/移除容器时，它会停留在“移除队列”或“死亡状态”中，这将被永久装载，并且不允许你运行下一步..因此，您必须使用下面的命令删除容器。这将杀死所有标记为死亡状态的容器。*

*解决方案:docker RM $(docker PS—all-q-f status = dead)*

# *错误号:18*

*错误:sendPeersProposal 承诺被拒绝:错误:名为“chain-code-name”的链码已经存在*

*情况—实例化链代码时发生*

*解决方案:此错误通常是由于链码已经在对等体上实例化而导致的。因此，摆脱这一点，删除 node_modules &做 npm 安装，一旦它完成好，重新启动“安装链码”，然后“instantitaet 链码”。*

*或者*

*使用不同的链节点 id*

# *错误号:19*

*outputBlock 上的 CRIT 008 错误:写入 genesis block: open 时出错。/channel-artifacts/genesis . block:没有这样的文件或目录*

*解决方案:在运行命令之前创建 channel-artifacts 文件夹*

*。/config txgen-profile fouorgsorderegenition-output block。/channel-artifact/genesis . block*

# *错误号:20*

*错误:readBlock -> DEBU 00a 获得状态:&{FORBIDDEN}错误:无法读取块:&{FORBIDDEN}*

*解决方案:确保在你启动你的网络之前没有其他容器在后面运行。发生此错误的原因是权限问题，即容器与现有容器冲突。Goahead，使用 docker rm -f $(docker ps -a -q)命令杀死容器并重启网络。*

# *错误号:21*

*grpc: addrConn.createTransport 无法连接到{ peer 0 . org 1 . example . com:7051 0<nil>}。错误:连接错误:desc =“传输:拨号时出错拨号 tcp:在 127.0.0.11:53 查找 peer0.org1.example.com:没有这样的主机”。正在重新连接…</nil>*

*解决方案:..待鉴定…*

# *错误号:22*

*错误:找不到网络问题*

*解决方案:转到。/peer-base.yaml，并将 CORE _ VM _ DOCKER _ host config _ network mode 更改为相应的网络名称。清洁码头集装箱，然后重建*

# *错误号:23*

*错误:发送实例化建议..
错误:[peer . js]:send proposal—45000 后超时*

*解决方案:…*

*注:一段时间后，它运行良好。但是当我再次面对它时，我会保持更新。*

# *错误号:24*

*错误:无法组合事务:ProposalResponsePayloads 不匹配-建议响应:版本:1 响应:<200/>*

*解决方案:确保在试图调用事务的对等体中安装了 Chaincode。为对等方设置环境变量。例如:*

*[CORE _ PEER _ MSPCONFIGPATH =/opt/gopath/src/github . com/hyperledger/fabric/PEER/crypto/PEER organizations/org 2 . example . com/users/Admin @ org 2 . example . com](mailto:CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com)/MSP CORE _ PEER _ ADDRESS = PEER 0 . org 2 . example . com:7051 CORE _ PEER _ LOCALMSPID = " org 2 MSP " CORE _ PEER _ TLS _ root cert _ FILE =/opt/gopath/src/github . com/hyperledger*

# *错误号:25*

*错误:调用注册端点失败，出现错误[错误:connect econrefused 127 . 0 . 0 . 1:7054]*

*解决方案:重新生成加密文件，然后将 secretKey 指向 ca-fabric 的 docker-composer-yaml 文件(如果 7054 指向 CA-fabric)*

# *错误号:26 实例化-链码- **实例化建议是错误的***

*当您尝试实例化 Chaincode 时，通常会出现此错误。*

***解决方案**:你应该先了解它的基本流程如何运作？，当您在对等体上安装 Chaincode 时，它通常会成功，但是在您安装和实例化 chaincode 之后，它会执行许多操作，例如*

*   *扫描链码语法、逻辑问题等。,*
*   *将代码捆绑在一起*
*   *尝试创建链代码名称为-链代码版本为的容器*

*因此，所发生的是，“链码”实例化通常会在“你的链码中的语法检查和逻辑问题”中失败..要对其进行识别，请在单独的终端中运行以下命令*

*码头工人日志-peer0.org1.example.com*

*在这里，peer 应该是您试图实例化的对象。这将给你清晰的日志，发生了什么，错误在哪里，修复错误&重新安装，即时更新..你应该可以走了*

# *错误 27:instantiate-chain code-错误:ChannelEventHub 已关闭*

*解决方案:这通常发生在“实例化链码”部分，当 eventhub 发现不止一个链码版本时，它将断开连接(当你用同一链码的多个版本升级时)。杀死所有的容器，只保留最新版本的，然后尝试安装并初始化链接代码*

# *错误 28:instantiate-chain code-错误:ChannelEventHub 已关闭*

*解决方案:这通常发生在“实例化链码”部分，当 eventhub 发现不止一个链码版本时，它将断开连接(当你用同一链码的多个版本升级时)。杀死所有的容器，只保留最新版本的，然后尝试安装并初始化链接代码*

## *错误 29:不能将 encKey(类型字符串)用作实体参数中的类型[]字节。新 256 加密实体*

# *错误 30:实例化建议是错误的—链代码*

*解决方案:当您发现此错误时，*

*I)使用以下命令检查您是否拥有正确的结构客户端和结构 ca 客户端*

*npm ls 结构-客户端*

*npm ls 结构-ca-客户端*

*确保您拥有与 fabric-client 版本相兼容的 docker 映像。*

***错误 31: docker:试图连接 unix 上的 docker 守护进程套接字时被拒绝***

*解决方案:sudo usermod -a -G docker $USER*

*在您的终端中执行此操作，关闭并重新打开终端。如果问题仍然存在，那么重新启动您的系统。*

*错误 32: [Remote.js]:错误:无法在截止日期前连接 URL:grpcs://localhost:7051*

***错误 33:** 响应::{"status":"BAD_REQUEST "，" info ":"授权更新时出错:验证读取集时出错:读取集预期的密钥[组]/通道/应用程序版本为 0，但得到的版本为 1"}*

*解决方案:大多数情况下，当您试图创建一个已经创建好的通道时，会出现这个错误。因此，您可以忽略此消息，继续“加入频道”*

***错误 34:** 响应::{"status":"FORBIDDEN "，" info ":"无法达到 1 个子策略的隐式阈值，需要剩余 1 个子策略:权限被拒绝" }*

***错误 35 :** 从属关系验证:无法获取从属关系“org3.department1”*

*解决方案:问题是示例中使用的 fabric-ca 不知道新的从属关系。默认情况下，fabric-ca 只有以下从属关系:*

*组织 1 .部门 1 组织 1 .部门 2 组织 2 .部门 1*

*转到 app/helper.js 并更新如下逻辑，将 getRegisteredUser 函数替换为以下逻辑。*

> *var getRegisteredUser = async function(username，useorg，isJson){
> try {
> var client = await getClientForOrg(useorg)；
> logger.debug('成功初始化了凭据存储')；
> //客户端现在可以代理组织 Org1
> //首先检查用户是否已经注册
> var user = await client . getuser context(username，true)；
> if(user&&user . is enrolled()){
> logger . info('从持久性成功加载成员')；
> var response = {
> 成功:假，
> }；
> 返回响应；
> } else {
> //用户未注册，因此我们需要一个 admin 用户对象来注册
> logger.info('用户%s 未注册，因此我们需要一个 admin 用户对象来注册'，username)；
> var admins = HFC . getconfigsetting(' admins ')；
> let admin user obj = await client . setuser context({ username:admins[0])。用户名和密码:admins[0]。secret })；*
> 
> *//新添加的
> if(adminuser obj . get affiliation()！= useorg . tolowercase()){
> logger . info('未注册管理关系。现在注册。);
> admin user obj . set affiliation(useorg . tolowercase())；
> adminuser obj . set roles([' peer '，' orderer '，' client '，' user '])；
> adminuser obj = await client . setuser context(adminuser obj)；
> }*
> 
> *logger.info('Admin User: %s '，adminuser obj)；
> let affiliation = useorg . tolowercase()+' department 1 '；*
> 
> *let ca client = client . getcertificate authority()；*
> 
> *//检查 org 是否存在
> const affiliation service = ca client . new affiliation service()；
> const registered affiliations = await affiliations service . get all(adminuser obj)；
> 如果(！registered affiliations . result . affiliations . some(x =>x . name = = useorg . tolowercase()){
> logger . info('注册新的附属:%s '，附属)；
> await affiliation service . create({ name:affiliation，force: true}，adminuser obj)；
> }*
> 
> *let secret = await ca client . register({
> enrollment id:username，
> affiliation:affiliation
> }，adminuser obj)；
> logger.debug('成功获取用户%s — %s '的密码，用户名)；
> user = await client . setuser context({用户名:用户名，密码:密码})；
> logger.debug('在客户端对象上成功注册用户名%s 和 setUserContext '，用户名)；
> 
> }
> if(user&&user . isen rolled){
> if(isJson&&isJson = = = true){
> var response = {
> success:true，
> secret: user。_enrollmentSecret，
> message:username+‘注册成功’，
> }；
> 返回响应；
> }
> } else {
> 抛出新错误(“用户未注册”)；
> }
> } catch(error){
> let message = '无法为组织'+useorg+'注册用户名'+ username +'，错误为:'+error . tostring()；
> logger.error(消息)；
> var response = {
> 成功:假，
> 消息:消息，
> }；
> 返回响应；
> }*
> 
> *};*

***错误 36 :** 无法获取注册用户:“用户”错误:错误:找不到模块。/utils.js '*

*未决问题:[https://jira.hyperledger.org/browse/FABN-1500](https://jira.hyperledger.org/browse/FABN-1500)*

*错误 37:死机:etcdraft 配置丢失[已恢复]*

***错误 38 :** 错误:无法组装事务，错误建议响应不成功，错误代码 500，消息错误启动容器:错误启动容器:无法生成平台特定的 docker 版本:无法提取 hyperledger/fabric-ccenv:最新:API 错误(404):找不到 hyperledger/fabric-ccenv 的清单:清单未知:清单未知*

*如果您运行的是早期版本的 fabric，您可能会遇到此错误。*

***解决方案**:在实例化链代码脚本中，在调用实例化之前添加这两行代码*

```
*docker pull hyperledger/fabric-ccenv:1.4.3docker tag hyperledger/fabric-ccenv:1.4.3 hyperledger/fabric-ccenv:latest*
```

***错误号 39** : [无法连接到 http://localhost:5984/_ utils](https://stackoverflow.com/questions/46727776/cannot-connect-to-http-localhost5984-utils)*

*解决方案:尝试从本地主机切换到 127.0.0.1*

***错误号 40** : [为组织](https://stackoverflow.com/questions/56828682/common-connection-profile-missing-error-while-adding-user-for-organization)添加用户时，公共连接配置文件丢失错误*

*解决方案:getCertificateAuthority()函数应使用 ca 名称传递*

*=====*

*Portainer 设置:*

```
*# docker volume create portainer_data
# docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer*
```

*— — — — — — — — — — — — — — — — — — — — — — — — — — — — — — -*

*注:喜欢这篇文章？给 Logeswaran 竖起大拇指(鼓掌)并在 Linkedin 上关注他*

*您是否有此处未列出的错误？请在下面的评论区添加。*

> *加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)*

## *另外，阅读*

*   *[复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)*
*   *[网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)*
*   *[最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)*
*   *开发人员的最佳加密 API*
*   *[密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)*
*   *最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)*
*   *[杠杆代币](/coinmonks/leveraged-token-3f5257808b22)终极指南*
*   *[最适合加密交易的 VPN](https://coincodecap.com/best-vpns-for-crypto-trading)*
*   *[用于 Huobi 的加密交易信号](https://coincodecap.com/huobi-crypto-trading-signals) | [HitBTC 审查](/coinmonks/hitbtc-review-c5143c5d53c2)*
*   *[TraderWagon 回顾](https://coincodecap.com/traderwagon-review) | [北海巨妖 vs 双子星 vs BitYard](https://coincodecap.com/kraken-vs-gemini-vs-bityard)*
*   *[如何在 FTX 交易所交易期货](https://coincodecap.com/ftx-futures-trading)*
*   *[OKEx vs KuCoin](https://coincodecap.com/okex-kucoin) | [摄氏替代度](https://coincodecap.com/celsius-alternatives) | [如何购买 VeChain](https://coincodecap.com/buy-vechain)*
*   *[3 commas vs . Pionex vs . crypto hopper](https://coincodecap.com/3commas-vs-pionex-vs-cryptohopper)*
*   *[如何使用 Cornix 交易机器人](https://coincodecap.com/cornix-trading-bot)*
*   *[Bitget 回顾](https://coincodecap.com/bitget-review)|[Gemini vs block fi](https://coincodecap.com/gemini-vs-blockfi)cmd |[OKEx 期货交易](https://coincodecap.com/okex-futures-trading)*
*   *[用信用卡购买密码的 10 个最佳地点](https://coincodecap.com/buy-crypto-with-credit-card)*