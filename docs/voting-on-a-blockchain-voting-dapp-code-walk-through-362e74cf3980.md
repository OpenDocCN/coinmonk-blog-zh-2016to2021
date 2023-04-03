# 区块链投票:投票 DApp 代码演练

> 原文：<https://medium.com/coinmonks/voting-on-a-blockchain-voting-dapp-code-walk-through-362e74cf3980?source=collection_archive---------3----------------------->

这是我写的 5 篇文章的最后一篇，解释以太坊上的端到端投票系统是如何工作的。在这一部分，我将解释 DApp 投票模块背后的代码。

![](img/e7bcbc4557295ba880e8da1619835fb3.png)

Photo by [Michael](https://unsplash.com/@michael75?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/vote?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

1.[区块链投票:它是如何工作的](/coinmonks/voting-on-a-blockchain-how-it-works-3bb41582f403)
2。[区块链投票:Solidity 合约代码详解](/coinmonks/voting-on-a-blockchain-solidity-contract-codes-explained-c677996d94f2)
3。[区块链投票:DApp 演示](/coinmonks/voting-on-a-blockchain-dapp-demonstration-dfb5944a0c9e)
4。[区块链投票:选票管理 DApp 代码走查](/coinmonks/voting-on-a-blockchain-ballot-management-dapp-code-walk-through-11d7d287d82e)
5。[区块链投票:投票 DApp 代码遍历](/@jacksonngtech/voting-on-a-blockchain-voting-dapp-code-walk-through-362e74cf3980)

今年 5 月，我受邀为新加坡一个名为“[新加坡未来学家](https://www.meetup.com/Singapore-Futurists/)”的团体做了一个关于区块链在民主社会中的作用的[演讲。我决定不只是谈论它，也许我应该演示一下区块链上的投票是如何工作的。这个演讲最终变成了这一系列的文章和一个我在我的](https://www.meetup.com/Singapore-Futurists/events/260805558/)[区块链研讨会](https://www.tp.edu.sg/courses/part-time-courses/skillsfuture/SkillsFuture-Series/finance-services-governance/beginners-introduction-to-blockchain-technology-with-ethereum-solidity-smart-contracts)中经常使用的现场演示。

**重述**

选票 DApp 的投票模块让投票者为主席的提议投票。这个模块的完整源代码可以在我的 [Github 库](https://github.com/jacksonng77/ballot)中找到

**设置**

```
**var** web3 = **new** Web3();
**var** accountaddress;
**var** ballotContract;
**var** ballotByteCode;
**var** Ballot;
**var** ballotABI = [{"constant":false,"inputs":[],..."voteDone","type":"event"}];

$( document ).ready(**function**() {
    $('#panels_contract').hide();
});
```

DApp 首先设置将在整个投票生命周期中使用的全局变量，即:

1.  `accountaddress`:这是管理投票过程的主席的以太坊账号。该地址在执行智能投票合同时使用。
2.  `ballotContract`:存储投票人将提供的选票智能契约的地址。主席将在启动投票流程后，向投票人提供选票智能合同的地址。
3.  `Ballot`:这个变量在我们执行和监控投票对象时存储它。
4.  `ballotABI`:(我在上面的代码片段中截断了它)这是选票智能合约的 ABI。

当页面加载时，我将`panels_contract`隐藏起来，因为此时投票者还没有提供他的钱包地址，投票的 DApp 没有义务告诉他提案是什么。

```
window.addEventListener('load', async () => {
     *// Modern dapp browsers...*
  	**if** (window.ethereum) {
     	    window.web3 = **new** Web3(ethereum);
       		**try** {
       		    *// Request account access if needed*
       		    await ethereum.enable();
       		    *// Acccounts now exposed*
       		    accountaddress = web3.givenProvider.selectedAddress;
                ballotContract = **new** web3.eth.Contract(ballotABI);        		    
      		} **catch** (error) {
       			*// User denied account access...*
       		}
       	}
       	*// Legacy dapp browsers...*
       	**else** **if** (window.web3) {
       		    window.web3 = **new** Web3(web3.currentProvider);
       		    *// Acccounts always exposed*
       		    web3.eth.sendTransaction({*/* ... */*});
       	}
       	*// Non-dapp browsers...*
       	**else** {
       			console.log('Non-Ethereum browser detected. You should consider trying MetaMask!');
       	}
});
```

当页面加载时，这段代码检查浏览器是否带有以太坊钱包，如 [MetaMask](https://metamask.io/) 。一旦找到，我们就将钱包的活动地址加载到`accountaddress` 变量中。我们还将投票合同的 ABI 加载到`ballotContract`变量中。

如果您的用户在没有以太坊钱包插件的浏览器中运行此 DApp，它会在您的浏览器控制台中弹出一条消息，告诉您它不能运行 DApp。在实时实现中，您可能希望将这些用户重定向到下载数字钱包的页面。

**展示提案**

```
**function** **getContract**(_ballotContractAddress){
     Ballot = **new** web3.eth.Contract(ballotABI, _ballotContractAddress);
     Ballot.methods.ballotOfficialName().call().then((result) => {
         $("#lbl_officialname").html("<b>Ballot Official Name: </b>" + result);
     });
     Ballot.methods.proposal().call().then((result) => {
         $("#lbl_proposal").html("<b>Proposal: </b>" + result);
     });

     loadFinalResult(Ballot);
     loadState(Ballot);

     watchVoteStarted(); *//start watching for events*
     watchVoteEnd(); *//start watching for vote end*
     watchVoteDone(); *//start watching for vote done*

     $('#panels_contract').show();
     $("#loader").hide();
     $("#section_voting").hide();
}
```

当投票人提供投票合同地址并按下[Go]按钮时，调用`getContract`函数。在这里，将加载投票合同，并显示投票官方名称和提案详细信息。

它执行`loadFinalResult()`来显示投票结果(如果投票已经完成)。它还执行`loadState()`来显示投票过程的当前状态——是处于创建、投票还是结束状态。

`watchVoteStarted()`、`watchVoteEnd()`和`watchVoteDone()` 函数将等待并观察投票智能合约触发这些事件。这些事件在投票过程中的不同点被触发。

**看着……看着**

```
**function** **watchVoteEnd**(){
     Ballot.events.voteEnded({
     }, (error, event) => { 
         console.log(event.returnValues.finalResult);
         loadState(Ballot);
         loadFinalResult(Ballot);
     })
     .on('data', (event) => {
     })
     .on('changed', (event) => {
     })
     .on('error', console.error)                      
 }
```

`watchVoteEnd()`等待投票智能合约触发`voteEnded`事件。当这种情况发生时，意味着主席已经结束了投票过程。`loadState()`执行以在用户界面中显示一条消息，告知投票人投票已经结束。`loadFinalResult()`执行向投票者显示结果。

```
**function** **watchVoteStarted**(){
     Ballot.events.voteStarted({
     }, (error, event) => { })
     .on('data', (event) => {
         console.log(event.event); 
         loadState(Ballot);
     })
     .on('changed', (event) => {
     })
     .on('error', console.error)
 }
```

`watchVoteStarted()` 等待投票智能合约触发`voteStarted`事件。当这种情况发生时，意味着主席已经开始投票程序。`loadState()` 在用户界面中显示一条消息，告诉投票人投票已经开始。

```
**function** **watchVoteDone**(){
    Ballot.events.voteDone({
    }, (error, event) => { 
        **var** strEventAddress = event.returnValues.voter.toString().toLowerCase();
        **var** strAccountAddress = accountaddress.toString().toLowerCase();

        console.log(strEventAddress);
        console.log(strAccountAddress);
        console.log(strEventAddress.valueOf() == strAccountAddress.valueOf())

        **if** (strEventAddress.valueOf() == strAccountAddress.valueOf()) {
            $("#loaderChoice").hide();
            loadState(Ballot);
         }
    })
    .on('data', (event) => {
    })
    .on('changed', (event) => {
    })
    .on('error', console.error)           
}
```

`watchVoteDone()`等待`voteDone`事件被触发。`voteDone`是选民投票时触发的。由于`voteDone`是在每次选民登记中的任何一个选民投票时触发的，所以代码会检查这个`voteDone`事件是否是由这个选民自己触发的，而不是碰巧同时投票的另一个选民。然后用`loadState()`重新加载投票状态，隐藏【是】和【否】按钮。

**加载选票信息**

```
async **function** **loadFinalResult**(myBallot){
    myBallot.methods.finalResult().call().then((result) => {
        $("#lbl_result").html("<b>Result: </b>" + result);
    });
}
```

`loadFinalResult()`从选票智能合约中获取最终投票结果，并将其显示在投票 DApp 的用户界面中。

```
async **function** **loadState**(myBallot){
    myBallot.methods.state().call().then((result) => {
        **if** (result == 0){
            $("#lbl_state").addClass("label label-primary");
            $("#lbl_state").html("Created"); 
        }
        **else** **if** (result == 1){
            $("#lbl_state").addClass("label label-success");
            $("#lbl_state").html("Voting"); 
        }                
        **else** **if** (result == 2){
            $("#lbl_state").addClass("label label-danger");
            $("#lbl_state").html("Ended");                    
        } 
        loadVoter(myBallot, accountaddress, result);
        **return** result;
    });
}
```

`loadState()`找出智能投票合同的当前状态，并在投票 DApp 的用户界面中显示“已创建”、“投票”或“已结束”。

```
async **function** **loadVoter**(myBallot, _myVoterAddress, _state){
    myBallot.methods.voterRegister(_myVoterAddress).call().then((result) => {
        **if** (result.voterName != "" && _state == 1) {
            $("#loaderChoice").hide();
            $("#section_voting").show();

            $("#lbl_voter").html("<b>Your Name: </b>" + result.voterName);
            console.log(result.voted);
            **if** (result.voted){
                $("#btnYes").hide();
                $("#btnNo").hide(); 
                $("#lblVoted").html("<span class='label label-primary'>Voted</span>");
            }
            **else** {
                $("#btnYes").show();
                $("#btnNo").show();                        
            }
        }
        **else**{
            $("#btnYes").hide();
            $("#btnNo").hide();                     
        }
    } );
}
```

`loadVoter()` 检查选票智能合约上的选民登记册，查看该选民的钱包地址是否已注册为选民。如果他不是，那他就没有资格投票。[是]和[否]按钮被隐藏。

它还检查投票智能契约的当前状态是否允许投票。如果是，则打开[是]和[否]按钮供投票人按下。

最后，它检查投票人之前是否已经投票。如果有，则[是]和[否]按钮被隐藏，投票只看到“已投票”通知。

**按钮**

```
$("#btnYes").click(async **function**(){
    $("#loaderChoice").show();

    *//estimate first*
    **var** mygas;
   	Ballot.methods.doVote(true).estimateGas({from: accountaddress})
    .then(**function**(gasAmount){
    mygas = gasAmount;
    })

   	Ballot.methods.doVote(true).send({
        from: accountaddress,
        gas: mygas, 
        gasPrice: web3.eth.gasPrice      	    
   	})
    .on('transactionHash', (hash) => { })
    .on('receipt', (receipt) => {
        console.log("done");
    })
    .on('confirmation', (confirmationNumber, receipt) => { })
    .on('error', console.error);
});$("#btnNo").click(async **function**(){
    $("#loaderChoice").show();
    *//estimate first*
    **var** mygas;
   	Ballot.methods.doVote(false).estimateGas({from: accountaddress})
    .then(**function**(gasAmount){
        mygas = gasAmount;
    })

  	Ballot.methods.doVote(false).send({
        from: accountaddress,
        gas: mygas, 
        gasPrice: web3.eth.gasPrice       	    
   	})
    .on('transactionHash', (hash) => { })
    .on('receipt', (receipt) => {
        console.log("done");
    })
    .on('confirmation', (confirmationNumber, receipt) => { })
    .on('error', console.error);
});
```

[是]和[否]按钮将执行选票智能合同上的`Ballot.methods.doVote(true)`和`Ballot.methods.doVote(false)`对提案进行投票。

```
$("#btnGo").click(async **function**() {	
    $("#loader").show();
    **var** _ballotAddress = $("#txtBallotAddress").val();
    getContract(_ballotAddress);
});
```

用户按下[Go]按钮加载选票智能合同。它执行`getContract()`让投票人看到提案并投票。

**回顾过去**

这就完成了对区块链的*投票。我非常喜欢开发投票 DApp 及其附带的这一系列文章。请务必将您的反馈发送给我。如果你发现了一个 bug，或者你已经分叉了我的代码，并从中构建了一些很酷的东西，请让我知道它如何变得更好。*

如果您喜欢本教程，也许您也希望阅读:

*   最小可行以太坊移动钱包:我为需要移动以太坊钱包的项目构建的基于 React Native 的模板。
*   [使用 Kaleido](/coinmonks/deploy-a-private-ethereum-blockchain-in-10-minutes-with-kaleido-73c21a26d5bb) 在 10 分钟内部署一个私有以太坊区块链:瞬间启动并运行一个私有以太坊区块链。
*   [通过演示解释智能合约](/coinmonks/smart-contract-explained-by-demonstration-93b06e938474):托管服务智能合约 DApp 的演示——在我看来，这是向外行人解释区块链是什么的最快方式。
*   [以太坊 IOT Kid Grounding Device](/coinmonks/internet-of-trusted-things-ethereum-kid-grounding-device-part-1-how-it-works-3cb1e8dd7db0) :我将区块链与物联网结合的尝试。
*   [彩票作为智能合约](/coinmonks/lottery-as-a-smart-contract-the-business-logic-3bd22d3a6c4e):分布式彩票，在以太坊区块链上构建彩票系统的尝试。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)[![](img/a06b758bdcc47dca7c2504f298674d87.png)](https://coincodecap.com)