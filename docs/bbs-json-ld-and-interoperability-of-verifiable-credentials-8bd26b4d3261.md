# BBS+、JSON-LD 与可验证凭证的互操作性

> 原文：<https://medium.com/coinmonks/bbs-json-ld-and-interoperability-of-verifiable-credentials-8bd26b4d3261?source=collection_archive---------4----------------------->

## 介绍

本文将帮助读者了解 JSON-LD、BBS+签名的概念，以及它们如何帮助构建可互操作的可验证凭证。

## JSON-LD

JSON-LD 代表链接数据的 JavaScript 对象符号。JSON-LD 有助于向 JSON 数据添加上下文。

让我们举一个例子来帮助我们理解，假设您正在构建一个 web 服务，它返回您站点上每个注册用户的名字和主页，您决定使用老式的 JSON 格式，如下所示

> {
> 
> 【姓名】:“约翰·史密斯”，
> 
> " home page ":" https://www . example . com/"
> 
> }

数据是可读的，非常容易集成到您的前端组件。您后来发现还有其他人提供类似的服务，并且您希望能够彼此交换数据。这里有一个问题，您正在使用的另一个服务具有如下所示的格式

> {
> 
> “姓名”:“约翰”，
> 
> " home page ":" https://www . example . com/"
> 
> }

正如我们所看到的，JSON 非常有表现力，但是我们可能会遇到不同服务的互操作性问题。在上面的例子中,“姓名”字段只返回名字。

如前所述，JSON-LD 为 JSON 中表示的数据提供了上下文。让我们来看一个我从维基百科上截取的例子。

> {
> " @ context ":{
> **" name "**:" http://xmlns . com/foaf/0.1/name "，
> **" home page "**:{
> " @ id ":" http://xmlns . com/foaf/0.1/work place home page "，
> "@type": "@id"
> }，
> **【Person】**:" http://XM

我们可以在示例中看到，有一个名为“@context”的新键，它有“name”、“homepage”、“Person”等字段，那么这些是什么意思呢？

从本质上讲，上下文部分帮助开发人员包括他在 JSON 内部的属性以及整个 JSON 对象本身时所遵循的规范。

在我们的例子中，我们看到对象现在有了一个类型(名为 Person ), Person 的规范被链接到上下文块中。该对象还有一个唯一的 id，可以帮助识别这个用户。

## BBS+签名

BBS+签名是一种多消息数字签名。通常，我们使用密钥对(私钥和公钥)中的数据密钥来签署消息，但是签名在整个消息上，因此我们不能共享消息的一部分来证明其真实性。多消息系统允许用私钥签名的消息被分解成更小的属性以供共享和验证。

与 CL 签名相比，BBS+签名更小更快(CL 签名是另一种类型的多消息签名，用于匿名凭证，并且基于 RSA)。

## JSON-LD 和 BBS+

BBS+提供了对选择性公开的支持，而 JSON-LD 帮助管理凭证内部的数据模式。

为了从凭证中请求属性[，使用了 JSON-LD 帧](https://www.w3.org/TR/json-ld11-framing/) 。由 BBS+签名创建的凭证和证明是自描述的，并且不需要分类帐(不像基于 CL 签名的系统，如 Hyperledger Indy)。

这种对分类帐依赖性的降低使它们成为构建可验证凭证的可互操作解决方案的理想选择。

其他解决的问题包括属性的排序，减少对发行者和验证者的设置要求。

下面是一个使用 BBS+JSON-LD 签名的例子，它摘自一个[开源实现](https://github.com/mattrglobal/jsonld-signatures-bbs)。

发行人已向持有人签发了以下凭证。

> {
> "[@ context](http://twitter.com/context)":[
> ""，
> "[https://w3id.org/citizenship/v1](https://w3id.org/citizenship/v1)"，
> "[https://w3id.org/security/bbs/v1](https://w3id.org/security/bbs/v1)"
> ，
> " id ":"[https://issuer.oidp.uscis.gov/credentials/83627465](https://issuer.oidp.uscis.gov/credentials/83627465)"，
> " type ":[" verifiable credential "，" PermanentResidentCard"]，
> "issuer": "did:示例:48939855、
> " issuanceDate ":" 2019–12–03t 12:19:52Z "、
> " expiration date ":" 2029–12–03t 12:19:52Z "、
> " credential subject ":{
> " id ":" did:example:b 34 ca 6 CD 37 bbf 23 "、
> "type": ["PermanentResident "，" Person"]、
> "givenName "base64，iVBORw0KGgokJggg== "，
> " resident since ": " 2015–01–01 "，
> "lprCategory": "C09 "，
> " lpr number ":" 999–999–999 "，
> " commutorclassification ":" C1 "，
> "出生地":"巴哈马群岛"，
> "出生日期":" 1958–07–1958

验证者向持有者发送以下请求(前面提到的 JSON-LD 帧)。

> {
> "[" @ context](http://twitter.com/context)":[
> "[https://www.w3.org/2018/credentials/v1](https://www.w3.org/2018/credentials/v1)"
> "[https://w3id.org/citizenship/v1](https://w3id.org/citizenship/v1)"，
> "[https://w3id.org/security/bbs/v1](https://w3id.org/security/bbs/v1)"
> ，
> "类型":["VerifiableCredential "，" PermanentResidentCard"]，
> "credentialSubject": {
> "类型":[" permanent resident resident "，" Person"]，【人】，【类型】。

正如我们所看到的，验证者只想要持有者的名、姓和性别。

持有者然后接受该请求并生成以下证明。

> {
> "[@ context](http://twitter.com/context)":[
> ""，
> "[https://w3id.org/citizenship/v1](https://w3id.org/citizenship/v1)"，
> "[https://w3id.org/security/bbs/v1](https://w3id.org/security/bbs/v1)"
> ，
> " id ":"[https://issuer.oidp.uscis.gov/credentials/83627465](https://issuer.oidp.uscis.gov/credentials/83627465)"，
> " type ":[" PermanentResidentCard "，" VerifiableCredential"]，
> "description ":"政府示例永久居民卡。"、
> "identifier": "83627465 "、
> "name ":"永久居民卡"、
> " credential subject ":{
> " id ":" did:example:b 34 ca 6 CD 37 bbf 23 "、
> "type": ["Person "、" PermanentResident"]、
> "familyName": "SMITH "、
> "gender": "Male "，
> " given name ":" JOHN "

可以看出，证明仅包含所请求的属性，仅此而已。

## 参考

下面列出的链接用于研究和认证，请求和证明如 [jsonld-singatures-bbs](https://github.com/mattrglobal/jsonld-signatures-bbs) 所述。

 [## JSON-LD -维基百科

### JSON-LD(链接数据的 JavaScript 对象符号)是一种使用 JSON 编码链接数据的方法。一个目标是…

en.wikipedia.org](https://en.wikipedia.org/wiki/JSON-LD) [](https://www.evernym.com/blog/bbs-verifiable-credentials/) [## 为什么可验证的证书社区应该汇聚在 BBS+上

### 由首席密码学架构师 Brent Zundel 于 2017 年夏天在 Evernym 开始。这是我的第一次创业，我…

www.evernym.com](https://www.evernym.com/blog/bbs-verifiable-credentials/)