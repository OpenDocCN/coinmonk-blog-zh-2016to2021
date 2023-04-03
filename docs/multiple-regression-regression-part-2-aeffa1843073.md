# 多元回归/回归第二部分

> 原文：<https://medium.com/coinmonks/multiple-regression-regression-part-2-aeffa1843073?source=collection_archive---------3----------------------->

正如我们在前面的后线性回归第 1 部分中所讨论的

[](https://dataneel.wordpress.com/2018/06/09/linear-regression-part-1/) [## 线性回归第一部分

### 线性回归是最简单的监督学习类型。回归分析的目的是探索…

dataneel.wordpress.com](https://dataneel.wordpress.com/2018/06/09/linear-regression-part-1/) 

当存在多组输入要素时，我们使用多元回归，如下式所示:

```
**Y=x0+(x1*w1+x2*w2+x3*w3+....+xn*wn)**
```

其中 x1，x2，x3，…xn 是输入特征。

然而，在现实世界中，像简单的线性回归那样处理二维数据并不简单。

## 我的意思是。

> 2D 数据具有 x 轴和 y 轴，这两个轴都具有一组值。如果您查看前面的示例, [HousePrice.csv](https://github.com/neelindresh/NeelBlog/blob/master/HousePrice.csv) 数据集，它包含 8 列。但是我们所做的是取两列，它们是*价格(旧)->x 轴*和*价格(新)- > Y 轴。*由此我们创建了我们的线性模型。然而，这不是真实世界的情况。在现实世界中，一个数据集可以有多个特征。正确预测一个值需要这些特性。

这就是我们正在做的:我们现在考虑所有的 8 列，而不是 8 列中的 2 列(记住日期列不是一个特性，它只是信息。所以我们可以忽略它)。这样我们就有 7 个特征作为 X 轴，1 个特征作为 Y 轴或 ***目标值。*** 下面给出代码。关于多重回归的任何信息，检查[线性回归部分:1](http://dataneel.wordpress.com)

```
**import** pandas
**import** numpy **as** np
#load csv file
df=pandas.read_csv('./DataSet/HousePrice.csv')
print(df.describe())
df=df.drop(['Date'],axis=1)
X=df[list(df.columns)[:-1]]
Y=df[list(df.columns)[-1]]
#print(X)
#print(Y)
**from** sklearn.model_selection **import** train_test_split
xtrain,xtest,ytrain,ytest=train_test_split(X,Y,random_state=0)
**from** sklearn.linear_model **import** LinearRegression
reg=LinearRegression()
reg.fit(xtrain,ytrain)
print(list(reg.predict(xtrain))[:5])
print(reg.score(xtest,ytest))
```

***使用熊猫库导入 CSV 文件:***

```
df=pandas.read_csv('./DataSet/HousePrice.csv')
print(df.describe())
```

> 熊猫。read_csv()从特定位置读取 csv 文件。其他选项也有参考: [Pandas.read_csv()](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_csv.html)

***丢弃不需要的数据(在本表中是['日期']列)***

```
df.drop(['Date'],axis=1)
```

> 这是删除“日期”后数据集中存在的特征。
> 
> *[|* 价格(全部)|变动(全部)|价格(新)|变动(新)|
> 
> 价格(现代)|变化(现代)|价格(旧)|变化(旧)| *]*

***定义 X(特征集)和 Y(目标)***

```
X=df[list(df.columns)[:-1]]
Y=df[list(df.columns)[-1]]
```

> list()->以列表格式转换列名

['价格(全部)'，'变更(全部)'，'价格(新)'，'变更(新)'，'价格(现代)'，'变更(现代)'，'价格(旧)']

> [:-1]->表示列表中最多第 n-1 个元素
> 
> [:1]->仅 lisy=t 中的第 n-1 个元素

> 因此，Y 或我们的目标特性成为‘价格(新)’,其余的成为我们的特性集或 X

X=['价格(全部)'，'变化(全部)'，'价格(新)'，'变化(新)'，'价格(现代)'，'变化(现代)'，'价格(旧)']

y =[更改(旧)]

```
**from** sklearn.model_selection **import** train_test_split
xtrain,xtest,ytrain,ytest=train_test_split(X,Y,random_state=0)
**from** sklearn.linear_model **import** LinearRegression
reg=LinearRegression()
reg.fit(xtrain,ytrain)
```

***trainttestsplit 将数据集划分为 75%训练 25%测试数据***

> xTrain，xTest，yTrain，yTest=train_test_split(X，Y)

***LinearRegression()。fit(X，Y)- >将 X 值和 Y 值分别放入给定函数***

> Lreg =线性回归()。fit(xTrain，yTrain)

***精度测量***

```
reg.predict(xTest)
Lreg.score(xTest,yTest)
```

***。score(预测值，测试数据的 Y 轴)*** 方法返回准确性得分或预测值与实际值匹配的百分比。

***。predict(测试数据的 x 轴)*** 返回 xTest 中每一项的预测值列表。

> 输出:

> ['价格(所有)'，'变化(所有)'，'价格(新)'，'变化(新)'，'价格(现代)'，'变化(现代)'，'价格(旧)'][T20，9.949265286326835，31，9 . 45653653565

如你所见，准确率为 99%

> 我想在这里包括多项式回归。但是它应该有一个自己的职位。所以我将展示另一个使用波士顿数据集的多元回归的例子

```
**#Code for Boston DataSet 
from** sklearn.datasets **import** load_boston
**from** sklearn.linear_model **import** LinearRegression
**from** sklearn.model_selection **import** train_test_split

boston = load_boston()
X = boston.data
print(type(X))
print(boston.keys())
print('Feature names:',boston['feature_names'])
Y = boston.target
X_train, X_test, y_train, y_test = train_test_split(X, Y, test_size=0.33, shuffle= **True**)

lineReg = LinearRegression()
lineReg.fit(X_train, y_train)
print(lineReg.score(X_test, y_test ))
```

***从 Scikit-Learn 数据集加载波士顿数据集:***

```
**from** sklearn.datasets **import** load_boston
boston=load_boston()
```

***定义 X 和 Y 并浏览数据集***

```
print(type(X))
print(boston.keys())
print('Feature names:',boston['feature_names'])X = boston.data
Y = boston.target
```

> 当您给 boston.keys()->这将返回波士顿数据的主要特征
> 
> 字典关键字(['特征名称'，'描述'，'目标'，'数据'])
> 
> ' feature _ names '-->数据集的功能名称或列，或简单地称为' X '
> 
> 描述每个功能的描述
> 
> 目标'->目标值或' Y '
> 
> 数据'->数据值

```
print('Feature names:',boston['feature_names'])
print('DESCR:',boston['DESCR'])print('Target ':',boston['target'])print('data:',boston['data'])
```

***和*** 同旧同旧线性回归

```
X_train, X_test, y_train, y_test = train_test_split(X, Y, test_size=0.33, shuffle= **True**)

lineReg = LinearRegression()
lineReg.fit(X_train, y_train)
print(lineReg.score(X_test, y_test ))
```

> 为你自己尝试一下

关注我的 youtube : python 教程和高级 python 编程

[](https://www.youtube.com/channel/UCTJE1mGfe5qgO5OfWE6surg?view_as=subscriber) [## 尼尔·巴塔查里亚

### 编程爱情

www.youtube.com](https://www.youtube.com/channel/UCTJE1mGfe5qgO5OfWE6surg?view_as=subscriber) 

关注我的博客

[](https://dataneel.wordpress.com/) [## 面向所有人的数据科学

### 在建:等到周三。谢谢你的耐心！！！线性回归是最简单的…

dataneel.wordpress.com](https://dataneel.wordpress.com/)