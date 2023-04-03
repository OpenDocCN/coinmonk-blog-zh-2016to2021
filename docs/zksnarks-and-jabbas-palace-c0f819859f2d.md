# ZKSnarks 和贾巴的宫殿

> 原文：<https://medium.com/coinmonks/zksnarks-and-jabbas-palace-c0f819859f2d?source=collection_archive---------0----------------------->

## 贾巴的保险箱

这篇文章将讨论贾巴的宫殿是如何对付各种通过使用 ZKSnarks 进入的星际渣滓的。贾巴的名字并不等同于信任，经常出现在他宫殿里的赏金猎人和地痞无赖更不值得信任。尽管贾巴的交易肆无忌惮，但他的锁箱生意却蒸蒸日上——可疑人物可以在这里存放他们不想被窥探的东西。贾巴雇佣我们设计一个安全系统，如果顾客有有效的锁箱密码，就可以进入宫殿。有趣的是，客户不应该给贾巴或他的同伙密码箱的安全码，而是一些识别码。安全系统应该能够在不知道用户密码的情况下验证用户。

我在网上搜索了一个很好的 zksnark 例子，发现了这两篇文章。我大量借鉴了内容来创作这篇文章

*   [https://media . consensys . net/introduction-to-zksnarks-with-examples-3283 b 554 fc3b](https://media.consensys.net/introduction-to-zksnarks-with-examples-3283b554fc3b)
*   https://asecuritysite.com/encryption/zksnark01

## ZKSnarks

术语 ZKSnark 代表*零知识简洁的非交互式知识论证*，用于证明某人知道一个秘密，而不需要他们分享秘密本身。进入贾巴的宫殿是 ZKSnark 的完美用法，因为主人可以证明他们有一个带锁的箱子，然后才被允许进入宫殿，而不需要分享锁箱本身的密码。

## 方程式

*生成器* —这个函数是 zksnark 过程的核心。它最终负责以零知识的方式运行验证逻辑。

*证明者* —这个帮助器函数是生成器提供给我们的，作为一种对验证者隐藏锁箱代码的方法，但是给了他们一个可以验证的替代值。这个函数的输出被称为证明，并被传递给验证者。

*Verifier —* 这个辅助函数也是生成器给我们的，作为验证证明者给我们的证明的一种手段。它将返回 true 或 false，以指示证明者是否已经验证了他们对已知秘密或有效锁箱代码的了解。

生成器采用逻辑函数(*条件)*和秘密参数(*λ*)，并返回*证明*函数和*验证*函数。

***注意，为了使系统可信，这些函数的底层代码应该对输入代码的人和接收证明的人可见。条件函数可以被逆向工程，因为它背后有逻辑，但真正的秘密是 lambda 参数。如果这个随机参数被泄露，那么系统将受到危害。***

我们将直接进入贾巴宫殿的例子，所以请做好准备…

## 情况

假设我们已经将所有的锁箱代码设计为 5 个一位数，并且我们知道所有的数字加在一起应该等于 13。所以，想象一个自行车密码锁，有 5 个数字可以选择 0-9。一个示例组合可以是 *11641。*注意，1+1+6+4+1 = 13。所以这是一个有效的密码，可以让我们进入贾巴的宫殿。我们的条件函数可以描述如下:

```
**function** requiredCondition(){
  **return** 13;
}
```

这显然不是超级安全的，因为它很容易被猜到——然而，这就是*λ*的用武之地。

```
**const** lambda = Math.floor((Math.random() * 1000000) + 1); //random number between 1 and a million
```

有了这两个输入，我们现在就可以创建生成器函数了

## 发电机

```
//random variable to make lockbox code difficult to guess
**const** lambda = Math.floor((Math.random() * 1000000) + 1); //random number between 1 and a million//logic for a lockbox code to be considered valid
**function** requiredCondition(){
  **return** 13;
}//generator implementation
**function** generator(condition, lambda) {
  **return** {
    proofFunction: (code)=>{**return** (code.reduce((a, b)=> a+b)) + lambda},
    verifyFunction: (proof)=>{**return** (condition() + lambda === proof)}
  };
}
```

瞧啊。看看我们如何通过要求锁箱总和等于 13 +一个介于 1 到 100 万之间的随机数来增加证明的难度。我们以后可能会改进这一点，但目前来说难度已经足够了。我们生成生成器客户端，传送到博巴-费特的巡洋舰和贾巴的宫殿。

```
//Generator usage - dispose of lambda by ionizing it
**const** generatorClient = generator(requiredCondition, lambda);//Send to bobba-fett
BountyChat().send(
'bobba-fett@hotmail.com',
"Bobba, Long time... Use this proofFunction to get into Jabba's",
generatorClient.proofFunction);//Send to Jabba's Palace
GangsterChat().send(
'jabba@hotmail.com',
"Jabba, Salutations... Use this verifyFunction for entry security",
generatorClient.verifyFunction);
```

**证明**

博巴-费特将证明函数加载到他的计算机中，并对代码进行一些诊断，以查看它是否可信。看起来没问题，所以他跳进超空间去看他的锁箱。他让巡洋舰着陆，把锁箱密码输入他手臂上的键盘，然后把生成的证明传送到贾巴前门的数据端口。

```
//Bobba enters his code of 11641
**const** proof = generatorClient.proofFunction([1,1,6,4,1]);
```

proof 函数生成一个有效值，因为他的代码加起来是 13。

**验证**

贾巴的安全主管已经将验证功能加载到他们的安全系统中，并用它来验证博巴的证据

```
//Verifies Bobba's proof of access Jabba's security system
const verified = verifyFunction(proof)
```

证据被证实是真的，博巴被允许进入宫殿，以便将他的密码输入他的锁箱。**注意，传递给保安的唯一值是 *13* ，他们无法知道每个锁箱的编号。**以下是完整代码:

```
//random variable to make lockbox code condition to guess
**const** lambda = Math.floor((Math.random() * 1000000) + 1); //random number between 1 and a million//logic for a lockbox code to be considered valid
**function** requiredCondition(){
  **return** 13;
}//generator implementation
**function** generator(condition, lambda) {
  **return** {
    proofFunction: (code)=>{**return** (code.reduce((a, b)=> a+b)) + lambda},
    verifyFunction: (proof)=>{**return** (condition() + lambda === proof)}
  };
}//Generator usage
**const** generatorClient = generator(requiredCondition, lambda);//Bobba enters his code of 11641
**const** lockBoxCode = [1,1,6,4,1];
**const** proof = generatorClient.proofFunction(lockBoxCode);//Security verifies it
**const** verified = generatorClient.verifyFunction(proof);**if**(verified) {
  console.log("Prover CAN prove that they know the values: "+lockBoxCode+" that total to: "+requiredCondition());
} **else** {
  console.log("Prover CANNOT prove that they know the values: "+lockBoxCode+" that total to: "+requiredCondition());
}
```

## 进攻！

这是一个积极的场景，让我们来看一个案例，一个卑鄙的家伙，格里多截获了我们发给博巴-费特的信息，并试图在没有有效锁箱的情况下进入宫殿。格里多可以读取发送的证明函数，但他不知道 lambda。他可以看到代码必须相加，但不知道有效的总和应该是多少。

```
(code)=>{**return** (code.reduce((a, b)=> a+b)) + lambda}
```

格里多试图输入以下值，在他第三次尝试后，他被安全部门抓获并喂给了兰克。

```
//Greedo enters his code of 66666
**const** lockBoxCode = [6,6,6,6,6];
**const** proof = generatorClient.proofFunction(lockBoxCode);
//failure 1//Greedo enters his code of 65432
**const** lockBoxCode = [6,5,4,3,2];
**const** proof = generatorClient.proofFunction(lockBoxCode);
//failure 2//Greedo enters his code of 12345
**const** lockBoxCode = [1,2,3,4,5];
**const** proof = generatorClient.proofFunction(lockBoxCode);
//failure 3
```

## 摘要

我们使用了一个 ZKSnark 来控制贾巴宫殿的入口，只允许那些拥有有效密码的角色进入。请注意，一旦角色进入宫殿，他们仍然必须将他们的代码输入到锁箱中才能访问其中的内容。贾巴似乎对这个解决方案很满意，并给予我们 2，000，000 信用点作为对我们努力的奖励。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)