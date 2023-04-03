# 以太坊上的智能工资合同

> 原文：<https://medium.com/coinmonks/payroll-smart-contract-on-ethereum-ae536e49403d?source=collection_archive---------1----------------------->

![](img/aec0ecff76e3a8f632bdb38f0730ef41.png)

在本帖中，我们将回顾使用 [Truffle](http://truffleframework.com/) 和 [Open Zeppelin](https://github.com/OpenZeppelin/zeppelin-solidity) 用 [Solidity](https://solidity.readthedocs.io/en/develop/) 编写的工资智能合同。你可以在这里找到回购[。](https://github.com/NFhbar/payroll)

让我们确保您在全球范围内安装了 Truffle:

```
$ npm install -g truffle
```

将我的 [repo](https://github.com/NFhbar/payroll) 克隆到您的本地机器并安装:

```
$ npm install
```

您现在可以使用 Truffle develop 来编译、迁移和运行测试:

```
$ truffle develop
truffle(develop)> compile
truffle(develop)> migrate
truffle(develop)> test
```

好了，说够了，让我们看一些代码。

该契约遵循类似于 Zeppelin 的 [ERC20](https://github.com/OpenZeppelin/zeppelin-solidity/blob/master/contracts/token/ERC20/ERC20.sol) 标准的接口方法，其中定义了接口契约。在我们的例子中，工资界面由下式给出:

您可以看到，我们拥有管理员工和支付所需的所有功能。现在，我们来看看主合同:`[Payroll.sol](https://github.com/NFhbar/payroll/blob/master/contracts/Payroll.sol)`。

`Payroll.sol`有三个导入:

```
import './InterfacePayroll.sol';
import './EmployeeStorage.sol';
import './EmployeeToken.sol';
```

允许设置存储变量:

```
/*
*  Storage
*/
InterfaceEmployeeStorage public employeeStorage;
EmployeeToken public employeeToken;
```

`EmployeeStorage.sol`定义了员工结构:

```
/*
*  Storage
*/
struct Employee {
     bool exists;
     uint256 id;
     address accountAddress; address tokenContractAddress;
     uint256 latestPayday;
     uint256 monthlySalary;
}
```

`EmployeeToken.sol`是一个简单的 ERC20 标准合同:

`Payroll.sol`的构造函数由下式给出:

```
/*
* Public functions
*/
/// [@dev](http://twitter.com/dev) Contract constructor sets storage and token.
function Payroll()
    public
{
   setEmployeeStorage(new EmployeeStorage());
   setEmployeeToken(new EmployeeToken());
}
```

它只是设置员工存储和员工令牌。合同所有者现在可以通过`InterfacePayroll.sol.`中定义的任何功能来管理员工。员工可以通过拨打以下电话请求在给定的支付期内获得报酬:

```
/// [@dev](http://twitter.com/dev) allows employee to get payed.
/// [@param](http://twitter.com/param) _payPeriod of requested payment.
function getPay (uint256 _payPeriod)
      public
      employeePayPeriodExists(_payPeriod)
      enoughTokensLeft()
{
      uint256 employeeSalary =        employeeStorage.getMonthlySalary(msg.sender);
      employeeToken.transfer(msg.sender, employeeSalary); uint256 newPayday = employeeStorage.getLatestPayday(msg.sender);
        newPayday++;
        employeeStorage.setlatestPayday(msg.sender, newPayday);
        EmployeePayed(msg.sender, newPayday--);}
```

`Payroll.sol`也有几个事件供 dapp 观看:

```
/*
*  Events
*/
event Deposit(address indexed sender, uint value);
event NewEmployee(address indexed newEmployee);
event EmployeeAddressChange(address indexed newAddress);event EmployeePaydayChange(address indexed employeeAddress, uint indexed newPayday);event EmployeeSalaryChange(address indexed employeeAddress, uint indexed newSalary);event EmployeeRemoved(address indexed employeeAddress, uint indexed_id);event EmployeePayed(address indexed employeeAddress, uint payPeriod);
```

现在工资支付期被设定为一个简单的`uint`，但它可以很容易地被设定为一个由所有者指定的实际时间框架。

就是这样！如果你更愿意在重新制作这份合同，我为你准备了一个[要点](https://gist.github.com/NFhbar/15a80e5785080e499099581799d3b5f2)供你使用。

> [直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)

非常感谢你阅读这篇文章！如果你觉得这篇文章很有用**，请点击这篇文章，并确保关注我的更多常规内容**，也请查看我的 [Github](https://github.com/NFhbar) ，因为我定期发布示例代码和项目。如果你有任何问题，欢迎在下面回复或者给我发邮件。

编码快乐！

[![](img/449450761cd76f44f9ae574333f9e9af.png)](http://bit.ly/2G71Sp7)