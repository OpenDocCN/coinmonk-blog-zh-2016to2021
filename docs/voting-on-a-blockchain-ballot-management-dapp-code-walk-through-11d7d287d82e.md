# 区块链投票:选票管理 DApp 代码演练

> 原文：<https://medium.com/coinmonks/voting-on-a-blockchain-ballot-management-dapp-code-walk-through-11d7d287d82e?source=collection_archive---------1----------------------->

这是探索以太坊上端到端投票系统开发的 5 篇文章中的第 4 篇。在本文中，我将解释 DApp 选票管理模块背后的代码。

![](img/f2817fafa9c5b7c3f7bbcd5022955553.png)

Photo by [Jesse Collins](https://unsplash.com/@jtc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/government?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在我撰写本系列的其余文章时，请经常查看。

1.[在区块链上投票:它是如何工作的](/coinmonks/voting-on-a-blockchain-how-it-works-3bb41582f403)
2。[区块链投票:Solidity 合约代码讲解](/coinmonks/voting-on-a-blockchain-solidity-contract-codes-explained-c677996d94f2)
3。[区块链投票:DApp 演示](/coinmonks/voting-on-a-blockchain-dapp-demonstration-dfb5944a0c9e)
4。[区块链投票:选票管理 DApp 代码走查](/@jacksonngtech/voting-on-a-blockchain-ballot-management-dapp-code-walk-through-11d7d287d82e)
5。[区块链投票:投票 DApp 代码遍历](/@jacksonngtech/voting-on-a-blockchain-voting-dapp-code-walk-through-362e74cf3980)

**重述**

选票 DApp 的[选票管理模块](https://jacksonng.org/codetest/ballot/)让主席设定提案、设置投票登记簿、打开/关闭选票并在投票时监控投票人。它还发布投票结果。这个模块的完整源代码可以在我的 [Github 库](https://github.com/jacksonng77/ballot/blob/master/dapp/index.html)中找到

**设置**

```
**var** web3 = **new** Web3();
        **var** accountaddress;
        **var** ballotContract;
        **var** ballotByteCode;
        **var** Ballot;
        **var** ballotABI = [{"constant":false,"inputs":[]....."type":"event"}];
        **var** voterTable;

        $( document ).ready(**function**() {
            $('#kaleidorefresh').hide();
            $('#panels_contract').hide();
            $('#panels_voters').hide();
       	    voterTable = $('#voterTable').DataTable( {
                columns: [
                    { title: "Address" },
                    { title: "Name" },
                    { title: "Status" }
                ]
            } );
        });
```

DApp 首先设置将在整个投票生命周期中使用的全局变量，即:

1.  `accountaddress`:这是管理投票过程的主席的以太坊账号。该地址在执行智能投票合同时使用。
2.  `ballotContract`:存储选票智能合约的地址，部署选票智能合约后将生成该地址。
3.  `ballotByteCode`:这里是选票智能合约的字节码 ekpt。稍后我们将在这个变量中填充字节代码。
4.  `Ballot`:这个变量在我们执行和监控投票对象时存储它。
5.  `ballotABI`:(我在上面的代码片段中截断了它)这是选票智能合约的 ABI。
6.  `voterTable`:这将存储一个数据表，我们在其中跟踪投票者、他们的地址和他们的投票状态。

当页面加载时，我们执行以下操作:

1.  隐藏显示投票合同信息的面板，因为在页面刚刚加载时，投票合同尚未部署。
2.  在这个阶段也隐藏显示投票者的面板。还没有任何选民。
3.  我还加载了[数据表](https://datatables.net/)，它在 3 列中显示了投票者的信息，即地址(包含投票者的以太坊地址)、姓名(这是他们的真实姓名，Zoe Ellie 等等。)和状态(是否已经投票)。

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
                    ballotByteCode = '0x608060....4052600080';

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
        **var** BallotContractAddress = "";
        **var** MyTransactionHash;
```

当页面加载时，这段代码检查浏览器是否带有以太坊钱包，如 [MetaMask](https://metamask.io/) 。一旦找到，我们就将钱包的活动地址加载到`accountaddress` 变量中。我们还将选票契约的 ABI 加载到变量`ballotContract`中，并将选票契约的字节码加载到变量`ballotByteCode`中。选票的字节码是在编译选票智能合约时获得的。

如果您的用户在没有以太坊钱包插件的浏览器中运行此 DApp，它会弹出一条控制台消息，告诉您不能运行 DApp。在实时实现中，您可能希望将这些用户重定向到下载数字钱包的页面。

**观望&等待**

在 DApp 的观察部分，我观察投票智能契约将在投票生命周期的不同点触发的各种事件。

```
**function** **watchVoteEnd**(){
            Ballot.events.voteEnded({
            }, (error, event) => { 
                console.log(event.returnValues.finalResult);
                loadState(Ballot);
                loadFinalResult(Ballot);
                $("#loaderStartVote").hide();
                $("#btnEnd").hide();
            })
            .on('data', (event) => {
            })
            .on('changed', (event) => {
                *// remove event from local database*
            })
            .on('error', console.error)                      
        }
```

在`watchVoteEnd()` 函数中，我观察并等待选票智能合约触发一个`voteEnded`事件。这表示投票已经结束，现在应该执行以下操作:

1.  执行`loadState()`将当前投票状态更改为“结束”。
2.  执行`loadFinalResult()`显示投票结果。
3.  隐藏旋转加载器动画 GIF 图像，因为投票已经结束，我们不再等待任何东西在后台加载。
4.  隐藏[结束投票]按钮，因为投票已经结束，所以不允许主席再次尝试结束投票。

```
**function** **watchVoteDone**(){
            Ballot.events.voteDone({
            }, (error, event) => { 
                console.log(event.returnValues.voter);
                updateNewVote(event.returnValues.voter);    
            })
            .on('data', (event) => {
            })
            .on('changed', (event) => {
                *// remove event from local database*
            })
            .on('error', console.error)           
        }
```

在`watchVoteDone()`函数中，一个投票者刚刚投了票。所以我们执行`updateNewVote()`来更新选民表，将他的状态从“未投票”改为“已投票”。

```
**var** lastVoteAdded="";
        **function** **watchVoterAdded**(){
            Ballot.events.voterAdded({
            }, (error, event) => { 
                console.log(event.returnValues.voter);
                loadTotalVoter(Ballot);

                *//strange hack: this event fires twice for some reasons*
                *//so I save the last voter address and suppress it if*
                *//it is the same as the previous one :P*
                **if** (lastVoteAdded != event.returnValues.voter){
                    loadVoter(Ballot, event.returnValues.voter);
                    lastVoteAdded = event.returnValues.voter;                    
                }
                $("#loaderNewVoter").hide();                
            })
            .on('data', (event) => {
            })
            .on('changed', (event) => {
                *// remove event from local database*
            })
            .on('error', console.error)
        }
```

在`watchVoterAdded()`函数中，我们观察并等待一个新的投票者被添加到投票者注册中。一旦添加了他，我们就停止旋转 loader GIF 图像，然后执行`loadTotalVoter()`来更新屏幕上显示的投票者总数。

```
**function** **watchVoteStarted**(){
            Ballot.events.voteStarted({
            }, (error, event) => { })
            .on('data', (event) => {
                console.log(event.event); *// same results as the optional callback above*
                $("#loaderStartVote").hide();
                $("#btnStart").hide();
                $("#btnEnd").show();
                $("#section_addVoter").hide();
                loadState(Ballot);
            })
            .on('changed', (event) => {
                *// remove event from local database*
            })
            .on('error', console.error)
        }
```

在`watchVoteStarted()` 函数中，我们观察由智能投票契约触发的事件，该事件表示投票已经开始。当主席按下[开始投票]时，就会出现这种情况。当这种情况发生时，我们:

1.  停止`loaderStartVote`旋转 GIF 图像，因为投票已经开始。
2.  我们隐藏了[开始投票]按钮，因为您不能重新启动已经开始的投票过程。
3.  我们显示[结束投票]按钮，因为现在可以结束已经开始的投票过程。
4.  我们隐藏了[添加投票人]按钮，因为它不再可能将投票人添加到已经开始的投票过程中。

**更新 DApp 的用户界面**

此部分中的功能通过从选票智能合同中读取相应的值并在那里显示它们来更新选票管理 DApp 用户界面的各个部分。

```
async **function** **loadBallotContract**(myBallotContractAddress){
        	Ballot = **new** web3.eth.Contract(ballotABI, myBallotContractAddress);
        	Ballot.methods.ballotOfficialName().call().then((result) => {
                $("#lbl_officialname").html("<b>Ballot Official Name: </b>" + result);
            });
            Ballot.methods.proposal().call().then((result) => {
                $("#lbl_proposal").html("<b>Proposal: </b>" + result);
            });

            loadFinalResult(Ballot);
            loadTotalVoter(Ballot);
            loadTotalVotes(Ballot);
            loadState(Ballot);

            $("#lbl_address").html("<b>Address: </b>" + myBallotContractAddress);           
        };
```

`loadBallotContract()` 功能从区块链中读取主席的姓名、提案和选票智能合约的地址，并将其显示在 DApp 用户界面的相应部分。它还从选票智能契约中加载投票过程的最终结果、投票者总数、投票总数以及投票过程的当前状态。

```
async **function** **loadFinalResult**(myBallot){
            myBallot.methods.finalResult().call().then((result) => {
                $("#lbl_result").html("<b>Result: </b>" + result);
            });
        }
```

`loadFinalResult()`功能从区块链上的选票智能合约中读取投票结果，并显示在 DApp UI 的相应部分。

```
async **function** **loadTotalVoter**(myBallot){
            myBallot.methods.totalVoter().call().then((result) => {
                $("#lbl_voters_num").html("<b>Voters: </b>" + result);
            });
        }
```

`loadTotalVoter()`功能从区块链上的选票智能合约中读取投票者总数的值，并显示在 DApp UI 的相应部分。

```
async **function** **loadTotalVotes**(myBallot){
            myBallot.methods.totalVote().call().then((result) => {
                $("#lbl_votes_num").html("<b>Votes: </b>" + result);
            });   
        }
```

`loadTotalVotes()`函数从区块链上的选票智能合约中读取总票数的值，并显示在 DApp UI 的相应部分。

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
                **return** result;
            });
        }
```

`loadState()`功能读取区块链上选票智能合约的当前状态，并将其显示在 DApp 用户界面的相应部分。投票过程有 3 种状态:

1.  *已创建:*投票提案已设置，投票合同刚刚初始化。
2.  *投票:*投票进行时
3.  *结束:*投票结束时

```
async **function** **loadVoter**(myBallot, _myVoterAddress){
            myBallot.methods.voterRegister(_myVoterAddress).call().then((result) => {
                console.log(result);

                **var** voteStatus;
                **if** (result.voted){
                    voteStatus = "<span class='label label-primary'>Voted</span>";
                }
                **else** {
                    voteStatus = "<span class='label label-danger'>Not Voted</span>";
                }

                **var** newRow = voterTable.row.add( [
                    _myVoterAddress,
                    result.voterName,
                    voteStatus
                ] ).draw(false).node();
                $('td:eq(2)', newRow).attr('id', _myVoterAddress+"_cell");

            } );
        }
```

`loadVoter()`函数从区块链上的选票智能合约中读取一个投票人的详细信息。它检查这个投票人是否已经投票。如果有，它会将其状态从“未投票”更改为“已投票”。它还检查这个投票者是否是新。如果是，他将被添加到投票登记表中。

```
**function** **updateNewVote**(_myVoterAddress){
            $("#" + _myVoterAddress+"_cell").html("<span class='label label-primary'>Voted</span>");  
            loadTotalVotes(Ballot);
        }
```

`updateNewVote()` 功能从区块链上的选票智能合同中读取投票人的投票状态，并在 DApp UI 的相应部分将其状态更改为“已投票”。

**按钮**

```
$("#btnEnd").click(async **function**(){
            $("#loaderStartVote").show();
            *//Ballot = new web3.eth.Contract(ballotABI, BallotContractAddress);*

            **var** mygas;
            Ballot.methods.endVote().estimateGas({from: accountaddress})
            .then(**function**(gasAmount){
                mygas = gasAmount;
            })

        	Ballot.methods.endVote().send({
                from: accountaddress,
                gas: mygas, 
                gasPrice: web3.eth.gasPrice        	    
        	})
            .on('transactionHash', (hash) => {
                console.log("a");
            })
            .on('receipt', (receipt) => {
                console.log("b");            

            })
            .on('confirmation', (confirmationNumber, receipt) => {
                console.log("c");
            })
            .on('error', console.error);            
        });
```

当投票过程主席准备结束投票时，按下[结束]按钮。它触发区块链中选票智能合约上的`endVote()`函数执行。

```
$("#btnStart").click(async **function**() {	
            $("#loaderStartVote").show();
            *//Ballot = new web3.eth.Contract(ballotABI, BallotContractAddress);*

            **var** mygas;
            Ballot.methods.startVote().estimateGas({from: accountaddress})
            .then(**function**(gasAmount){
                mygas = gasAmount;
            })

        	Ballot.methods.startVote().send({
                from: accountaddress,
                gas: mygas, 
                gasPrice: web3.eth.gasPrice        	    
        	})
            .on('transactionHash', (hash) => {
                console.log("a");
            })
            .on('receipt', (receipt) => {
                console.log("b");            

            })
            .on('confirmation', (confirmationNumber, receipt) => {
                console.log("c");
            })
            .on('error', console.error);
        });
```

当投票过程主席准备好开始投票时，按下[开始]按钮。它触发区块链中选票智能合约上的`startVote()`功能执行。

```
$("#btnAdd").click(async **function**() {	
            $("#loaderNewVoter").show();
            console.log($("#txtNewVoterAddress").val());
            console.log($("#txtNewVoterName").val());

            *//Ballot = new web3.eth.Contract(ballotABI, BallotContractAddress);*

            *//estimate first*
            **var** mygas;
            Ballot.methods.addVoter($("#txtNewVoterAddress").val(), $("#txtNewVoterName").val()).estimateGas({from: accountaddress})
            .then(**function**(gasAmount){
                mygas = gasAmount;
            })

        	Ballot.methods.addVoter($("#txtNewVoterAddress").val(), $("#txtNewVoterName").val()).send({
                from: accountaddress,
                gas: mygas, 
                gasPrice: web3.eth.gasPrice       	    
        	})
            .on('transactionHash', (hash) => {
                console.log("a");
            })
            .on('receipt', (receipt) => {
                console.log("b");            

            })
            .on('confirmation', (confirmationNumber, receipt) => {
                console.log("c");
            })
            .on('error', console.error);

        });
```

当投票程序主席向投票登记簿添加新投票人时，按下[添加投票人]按钮。它触发区块链中选票智能合约上的`addVoter()`函数执行。

```
$("#btnGo").click(async **function**() {	
            $("#loader").show();
            **var** i = 0;
            **var** _ballotOfficialName = $("#official").val();
            **var** _proposal = $("#proposal").val();

            ballotContract.deploy({
                data: ballotByteCode,
                arguments: [_ballotOfficialName, _proposal],
            })
            .send({
                from: accountaddress,
                gas: 1308700, 
                gasPrice: web3.eth.gasPrice,
                gasLimit: 2000000
            }, (error, transactionHash) => {
                MyTransactionHash = transactionHash;
                *//getContract(); for private kaleido chain only*
            })
            .on('error', (error) => { 
                console.log("b");            
            })
            .on('transactionHash', (transactionHash) => { 
                console.log("c");
            })
            .on('receipt', (receipt) => {
                console.log("DONE" + receipt.contractAddress); *// contains the new contract address*

                BallotContractAddress = receipt.contractAddress;
                loadBallotContract(BallotContractAddress);
                console.log(BallotContractAddress);
                $("#contractAddress").val(BallotContractAddress);
                watchVoteStarted(); *//start watching for events*
                watchVoterAdded(); *//start watching for new voters*
                watchVoteDone(); *//start watching for vote done*
                watchVoteEnd(); *//start watching for vote end*
                $('#panels_contract').show();
                $('#panels_voters').show();
                $("#btnStart").show();
                $("#btnEnd").hide();
                $("#loader").hide();
                $("#section_addVoter").show();
            })
            .on('confirmation', (confirmationNumber, receipt) => { 
                console.log(i);
                i++;
            })
            .then((newContractInstance) => {
                console.log(newContractInstance.options.address) *// instance with the new contract address*
            });

        });
```

当主席开始新的投票过程时，按下[Go]按钮。它触发了在区块链部署的新的智能投票合同。

在成功部署后，它用刚刚在区块链上部署的新选票智能合同的地址填充`BallotContracAddress`字段。它执行`loadBalloContract()`来更新 DApp UI 上的投票官方名称和提议。然后，它会在投票开始时观察投票契约触发的新事件。

它使合同面板可见，以便用户可以查看合同详细信息。它使选民面板可见，以便主席可以添加新的选民。它还显示了[开始投票]按钮，以便主席可以按下它来宣布投票已经开始。

它会禁用[结束投票]按钮，因为不先开始投票就无法结束投票过程。

它还显示了显示选民登记册中的选民的部分，以便主席可以开始将选民添加到选票智能合同中。

**下一步是什么**

在区块链上**投票的[第五(也是最后)](/@jacksonngtech/voting-on-a-blockchain-voting-dapp-code-walk-through-362e74cf3980)部分，我们将检查投票者 DApp 模块的代码。敬请关注。**

[![](img/a06b758bdcc47dca7c2504f298674d87.png)](https://coincodecap.com)

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)