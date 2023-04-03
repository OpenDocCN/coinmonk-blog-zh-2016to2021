# 建造一个联邦币

> 原文：<https://medium.com/coinmonks/building-a-fedcoin-4577eccc9ff0?source=collection_archive---------6----------------------->

## *通过世界上首批区块链管理系统之一 BlockCluster 的创始人兼首席技术官 Narayan Prusty 的这篇文章，了解如何构建 FedCoin。*

![](img/d91ed12b52b427f22e46294a67fe4da6.png)

FedCoin 是一种由中央银行发行的数字货币，与他们的法定货币一对一对冲。使用区块链将法定货币数字化有几个好处，比如方便跨境支付和节省对账工作。

本文将向您展示如何在两个不同的区块链网络上构建数字化的印度卢比和美元。然后，您将创建一些原子互换合约，以自动实现银行之间的货币交换。这个用例要求您使用 IBFT 共识创建两个不同的法定人数网络。

在每一个网络中，都有一个权威，就是央行， ***N*** 个同行，就是其他银行。所以，你可以假设在第一个网络中，**美联储系统** ( **FRS** )是权威，而**美银** ( **BOA** )和 ICICI 银行是对等。同样，在第二个网络中，印度储备银行是权威机构，印度银行和印度工业信贷银行是对等机构。

您现在不必构建这个网络，因为在构建和测试智能合约时，您可以只使用一个具有四个以太坊帐户地址的节点。这将足以模拟整个场景。

## 智能合约将法定货币数字化

这是一个在区块链上创建数字化美元的基本智能合约。这种智能合约允许您发行和转移数字化货币:

```
pragma solidity ^0.4.19;contract USD {mapping (address => uint) balances;
 mapping (address => mapping (address => uint)) allowed;
 address owner;function USD() {
  owner = msg.sender;
 }function issueUSD(address to, uint amount) {
 if(msg.sender == owner) {
  balances[to] += amount;
  }
 }function transferUSD(address to, uint amount) {
  if(balances[msg.sender] >= amount) {
   balances[msg.sender] -= amount;
   balances[to] += amount;
  }
 }function getUSDBalance(address account) view returns (uint balance) {
   return balances[account];
 }function approve(address spender, uint amount) {
   allowed[spender][msg.sender] = amount;
 }function transferUSDFrom(address from, address to, uint amount) {

  if(allowed[msg.sender][from] >= amount && balances[from] >= amount) {
    allowed[msg.sender][from] -= amount;
    balances[from] -= amount;
    balances[to] += amount;
  }
 }
}
```

以下是上述代码的工作原理:

首先，定义一个映射来存储每家银行持有的美元数量。每个银行可以有多个地址来实现隐私。这些地址不一定是银行。它们也可以是其他智能合约，因为每个智能合约也有`address`。

接下来，你假设中央银行部署合约。因此，您通过将中央银行的`address`分配给`owner`来将中央银行定义为发行人。

然后，您定义函数`issueUSD`，通过该函数，央行可以向其他银行发行美元。

然后，您定义另一个名为`transferUSD`的函数，银行可以通过它在彼此之间转移美元。

接下来，您有一个读取帐户余额的函数。

最后，你有两个重要的函数:`approve`和`transferUSDFrom`。`transferUSDFrom`功能允许合约代表你发送美元。换句话说，您正在为同一区块链上的其他智能合约提供 API 来管理您的资金。`approve`功能用于批准管理您资金的智能合同。在给`approve`打电话时，你提到合同能管理你多少资金。

现在，部署一个类似的合同，在第二个网络中数字化 INR。将之前合同中的`USD`替换为`INR`并部署。它应该是这样的:

```
pragma solidity ^0.4.19;contract INR {
 mapping (address => uint) balances;
 mapping (address => mapping (address => uint)) allowed;
 address owner;

 function INR() {
  owner = msg.sender;
 }function issueINR(address to, uint amount) {
  if(msg.sender == owner) {
  balances[to] += amount;
  }
 }function transferINR(address to, uint amount) {
  if(balances[msg.sender] >= amount) {
   balances[msg.sender] -= amount;
   balances[to] += amount;
  }
 }function getINRBalance(address account) view returns (uint balance) {
  return balances[account];
 }function approve(address spender, uint amount) {
  allowed[spender][msg.sender] = amount;
 }

 function transferINRFrom(address from, address to, uint amount) {
  if(allowed[msg.sender][from] >= amount && balances[from]>= amount) {
   allowed[msg.sender][from] -= amount;
   balances[from] -= amount;
   balances[to] += amount;
  }
 }
}
```

## 原子互换智能合约

你成功地数字化了法定货币。现在是时候实现原子交换智能契约了，它将提供哈希锁定机制。您在每个区块链都部署了原子互换智能合约，也就是说，第一个区块链上的原子互换智能合约将锁定美元一段时间，并期望一家印度银行(此处为 ICICI 银行)在定义的时间段内使用该秘密来认领它。

类似地，第二个区块链上的原子互换合同将锁定 INR 一段时间，并期望一家美国银行(这里是 BOA)使用该秘密在规定的时间内认领它。

以下是锁定美元的原子互换智能合约:

```
pragma solidity ^0.4.19;

import "./USD.sol";

contract AtomicSwap_USD {

    struct AtomicTxn {
        address from;
        address to;
        uint lockPeriod;
        uint amount;
    }

    mapping (bytes32 => AtomicTxn) txns;
    USD USDContract;

    event usdLocked(address to, bytes32 hash, uint expiryTime, 
      uint amount);
    event usdUnlocked(bytes32 hash);
    event usdClaimed(string secret, address from, bytes32 hash);

    function AtomicSwap_USD(address usdContractAddress) {
        USDContract = USD(usdContractAddress); 
    }

    function lock(address to, bytes32 hash, uint lockExpiryMinutes,
      uint amount) {
        USDContract.transferUSDFrom(msg.sender, address(this), amount);
        txns[hash] = AtomicTxn(msg.sender, to, block.timestamp + 
         (lockExpiryMinutes * 60), amount);
        usdLocked(to, hash, block.timestamp + (lockExpiryMinutes * 60),
          amount);
    }

    function unlock(bytes32 hash) {
        if(txns[hash].lockPeriod < block.timestamp) {
            USDContract.transferUSD(txns[hash].from, 
              txns[hash].amount);
            usdUnlocked(hash);
        }
    }

    function claim(string secret) {
        bytes32 hash = sha256(secret);
        USDContract.transferUSD(txns[hash].to, txns[hash].amount);
        usdClaimed(secret, txns[hash].from, hash);
    }

    function calculateHash(string secret) returns (bytes32 result) {
        return sha256(secret);
    }
}
```

下面是前面的智能合约的工作原理:

在部署智能契约时，您提供了`USD`契约的契约地址，以便它可以调用其函数来转移资金。

`lock`方法用于使用`hash`锁定资金。显然，在调用`lock`方法之前，BOA 必须批准这个原子互换契约地址，以便能够访问其一定数量的资金。需要`hash`，锁定一定时间的资金。`amount`指定要锁定多少美元，该金额应小于或等于批准的金额。`to`地址指定了印度银行的地址——即 ICICI 银行。所以，当 ICICI 银行来认领资金时，他们会去这个地址。这个函数实际上将资金转移到其合同地址(即`address(this)`)并触发一个事件，以便 ICICI 银行可以看到资金已经被锁定。

在`hash`到期后，如果资金无人认领，BOA 可使用`unlock`方法解锁资金。

ICICI 银行使用`claim`方法来要求使用秘密的资金。

最后，你用`calculateHash`的方法来计算一个秘密的`hash`。

将前面合同中的`USD`更改为`INR`，为第二个区块链提供原子互换智能合同。下面是代码的样子:

```
pragma solidity ^0.4.19;

import "./INR.sol";

contract AtomicSwap_INR {

    struct AtomicTxn {
        address from;
        address to;
        uint lockPeriod;
        uint amount;
    }

    mapping (bytes32 => AtomicTxn) txns;
    INR INRContract;

    event inrLocked(address to, bytes32 hash, uint expiryTime,
      uint amount);
    event inrUnlocked(bytes32 hash);
    event inrClaimed(string secret, address from, bytes32 hash);

    function AtomicSwap_INR(address inrContractAddress) {
        INRContract = INR(inrContractAddress); 
    }

    function lock(address to, bytes32 hash, uint lockExpiryMinutes, 
      uint amount) {
        INRContract.transferINRFrom(msg.sender, address(this), amount);
        txns[hash] = AtomicTxn(msg.sender, to, block.timestamp + 
         (lockExpiryMinutes * 60), amount);
        inrLocked(to, hash, block.timestamp + (lockExpiryMinutes * 60), 
          amount);
    }

    function unlock(bytes32 hash) {
        if(txns[hash].lockPeriod < block.timestamp) {
            INRContract.transferINR(txns[hash].from, 
              txns[hash].amount);
            inrUnlocked(hash);
        }
    }

    function claim(string secret) {
        bytes32 hash = sha256(secret);
        INRContract.transferINR(txns[hash].to, txns[hash].amount);
        inrClaimed(secret, txns[hash].from, hash);
    }

    function calculateHash(string secret) returns (bytes32 result) {
        return sha256(secret);
    }
}
```

## 测试

现在，您已经准备好在两个不同区块链的资产之间进行原子互换的智能合约。接下来，编写一些 JavaScript 代码来测试前面的契约并进行原子交换。下面的代码允许您这样做。出于测试和模拟目的，您可以在一个具有四个帐户的仲裁节点中运行以下代码:

```
var generateSecret = function () {
    return Math.random().toString(36).substr(2, 9);
};

var web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));

var RBI_Address = "0x92764a01c43ca175c0d2de145947d6387205c655";
var FRS_Address = "0xbc37e7ba9f099ba8c61532c6fce157072798fe77";
var BOA_Address = "0x104803ea6d8696afa6e7a284a46a1e71553fcf12";
var ICICI_Address = "0x84d2dab0d783dd84c40d04692e303b19fa49bf47";

var usdContract_ABI = /* Put JSON here */;
var usdContract_Bytecode = "0x606..."
var atomicswapUSD_ABI = /* Put JSON here */;
var atomicswapUSD_Bytecode = "0x606..."
var inrContract_ABI = /* Put JSON here */;
var inrContract_Bytecode = "0x606..."
var atomicswapINR_ABI = /* Put JSON here */;
var atomicswapINR_Bytecode = "0x606..."

var usdContract = web3.eth.contract(usdContract_ABI);
var usd = usdContract.new({
  from: FRS_Address, 
   data: usdContract_Bytecode, 
   gas: "4700000"
}, function (e, contract){
  if (typeof contract.address !== 'undefined') {
    var usdContractAddress = contract.address;
    var usdContractInstance = usdContract.at(usdContractAddress)
    var atomicswap_usdContract = web3.eth.contract(atomicswapUSD_ABI);
    var atomicswap_usd = atomicswap_usdContract.new(usdContractAddress, {
        from: FRS_Address, 
        data: atomicswapUSD_Bytecode, 
        gas: "4700000"
    }, function (e, contract){
        if (typeof contract.address !== 'undefined') {
            var atomicSwapUSDAddress = contract.address;
            var atomicSwapUSDContractInstance =
              atomicswap_usdContract.at(atomicSwapUSDAddress);

            var inrContract = web3.eth.contract(inrContract_ABI);

        var inr = inrContract.new({
            from: RBI_Address, 
            data: inrContract_Bytecode, 
            gas: "4700000"
        }, function (e, contract){
            if(typeof contract.address !== 'undefined') {
                var inrContractAddress = contract.address;
                var inrContractInstance = 
                  inrContract.at(inrContractAddress)
            var atomicswap_inrContract =
              web3.eth.contract(atomicswapINR_ABI);
            var atomicswap_inr = atomicswap_inrContract.new(
                inrContractAddress, {
                from: RBI_Address, 
                data: atomicswapINR_Bytecode, 
                gas: '4700000'
            }, function (e, contract){
                if (typeof contract.address !== 'undefined') {
                    var atomicSwapINRAddress = contract.address;
                    var atomicSwapINRContractInstance = 
                      atomicswap_inrContract.at(atomicSwapINRAddress);

                }
            })
            }
        })
        }
    })
  }
})
```

首先，您部署了`USD`契约，然后通过将`USD`契约的地址作为参数进行传递，部署了 USD 的原子互换契约。您将这些合同部署为 FRS。然后，通过将`INR`契约的地址作为参数进行传递，为 INR 部署`INR`契约和原子交换契约。您将这些合同部署为 RBI。

将以下代码放在提到延续的地方:

```
//Issue USD
usdContractInstance.issueUSD.sendTransaction(BOA_Address, 1000,
  {from: FRS_Address}, function(e, txnHash){

  //Fetch USD Balance
  console.log("Bank of America's USD Balance is : " + 
    usdContractInstance.getUSDBalance.call(BOA_Address).toString())

  //Issue INR
  inrContractInstance.issueINR.sendTransaction(ICICI_Address, 1000,
   {from: RBI_Address}, function(e, txnHash){

    //Fetch INR Balance
    console.log("ICICI Bank's INR Balance is : " + 
      inrContractInstance.getINRBalance.call(ICICI_Address).toString())

    //Generate Secret and Hash
    var secret = generateSecret();
    var hash = atomicSwapUSDContractInstance.calculateHash.call(secret,
      {from: BOA_Address});

    //Give Access to Smart Contract
    usdContractInstance.approve.sendTransaction(atomicSwapUSDAddress,
      1000, {from: BOA_Address}, function(e, txnHash){

      //Give Access to Smart Contract
      inrContractInstance.approve.sendTransaction(atomicSwapINRAddress,
        1000, {from: ICICI_Address}, function(e, txnHash){

        //Lock 1000 USD for 30 min
        atomicSwapUSDContractInstance.lock.sendTransaction(ICICI_Address, hash, 
  30, 1000, {from: BOA_Address, gas: 4712388}, function(e, txnHash){

          //Fetch USD Balance
          console.log("USD Atomic Exchange Smart Contracts holds : " + 
            usdContractInstance.getUSDBalance.call
            (atomicSwapUSDAddress).toString())

          //Lock 1000 INR for 15 min
          atomicSwapINRContractInstance.lock.sendTransaction(BOA_Address,
  hash, 15, 1000, {from: ICICI_Address, gas: 4712388},
  function(e, txnHash){

            //Fetch INR Balance
            console.log("INR Atomic Exchange Smart Contracts holds : "
              + inrContractInstance.getINRBalance.call
              (atomicSwapINRAddress).toString())

            atomicSwapINRContractInstance.claim(secret, {
              from: BOA_Address, gas: 4712388
            }, function(error, txnHash){

              //Fetch INR Balance
              console.log("Bank of America's INR Balance is : " +
                inrContractInstance.getINRBalance.call
                (BOA_Address).toString())

              atomicSwapUSDContractInstance.claim(secret, {
                from: ICICI_Address, gas: 4712388
              }, function(error, txnHash){

                //Fetch USD Balance
                console.log("ICICI Bank's USD Balance is : " +
                  usdContractInstance.getUSDBalance.call
                  (ICICI_Address).toString())
              })

            })

          })
        })
      })

    })
  })

})
```

以下是上述代码的工作原理:

1.这里，FRS 向 BOA 发行美元，然后 RBI 向 ICICI 银行发行印度卢比。

2.然后，宝儿产生了一个秘密。您使用一个非常基本的函数来生成一个秘密。显然，在现实世界的场景中，您应该使用某种基于硬件的工具来生成这些安全的秘密。

3.接下来，计算秘密的散列。

4.美国银行和 ICICI 银行分别向美元原子掉期和印度卢比原子合约提供资金。

5.美国银行在美元原子掉期合约中锁定美元 30 分钟，并声明只有 ICICI 银行可以要求资金。

6.同样，ICICI 银行在 INR 原子掉期合约中锁定 INR 15 分钟，并声明只有 BOA 可以申请资金。

7.最后，BOA 继续要求 INR。ICICI 一知道这个秘密，它就开始行动，要求赔偿美元。

为了测试前面的契约，首先，复制您的以太坊地址并替换在前面的例子中已经生成的地址。然后，确保您解锁了节点中的所有四个帐户。最后，编译契约并填充`ABI`和`Bytecode`变量。

*如果你觉得这篇文章有趣，你可以探索 Narayan Prusty 的* [区块链 for Enterprise](https://www.amazon.com/Blockchain-Enterprise-applications-interoperability-permissioned/dp/1788479742) *在你选择的使用以太坊的领域中实现区块链原则。* [面向企业的区块链](https://www.packtpub.com/big-data-and-business-intelligence/blockchain-enterprise) *涵盖了面向企业和开发者的区块链的方方面面，通过真实世界的例子，可以帮助你理解 DApps 是什么以及区块链生态系统是如何运作的。*

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)