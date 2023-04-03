# 将缩短的(短信/推文/帖子)俚语和单词缩写改造成句子 NLP

> 原文：<https://medium.com/coinmonks/remaking-of-shortened-sms-tweet-post-slangs-and-word-contraction-into-sentences-nlp-7bd1bbc6fcff?source=collection_archive---------1----------------------->

![](img/08cde8f27b6833404093ff03881d78ad.png)

NLP 中最大的问题是在推文或帖子中缩短单词，如**但 to bt，for to fr，而不是 to nt** ， **thre to there** 等。现在最大的问题是，没有这样的词典可以将所有的俚语词映射到它们原来的英语对应词上。

但是对于情感分析，我们需要完整的句子和适当的含义，否则情感可能不会像我们预期的那样出现。

我们举个例子了解一下。

> 1.只有他告诉他的情妇他爱她。(别人都没有)
> 2。他只告诉他的情妇他爱她。(他没*秀*她)
> 3。他只告诉他的情妇他爱她。(对其他人保密)。他告诉他唯一的情妇他爱她。(强调他只有一个！)
> 5。他只告诉他的情妇他爱她。(没告诉她别的)
> 6。他告诉他的情妇，只有他爱她。(“我是你的全部，亲爱的——没有人想要你。”)
> 7。他告诉他的情妇他只爱她。(并不是说他想娶她。)
> 8。他告诉他的情妇他只爱她。(是啊，不都是……)。

在上面的句子中,“only”的位置完全改变了句子的意思。所以基本上我们需要句子的语义完整，这样我们才能正确的情绪。

# 更正 SMS 俚语:

在我没完没了地寻找如何纠正这一点的时候。我遇到了许多解决方案。让我们一个一个地试一试。

1.  **字典**:

我们可以创建一个缩略俚语的字典，并用它们来替换原来单词的缩略文本。

我做了两个文件，一个是缩写，另一个是短信俚语。

1.1.我在网上找到的压缩文件，它的作用是将“不”改为“不”，将“不能拥有”改为“不能拥有”等等。

1.  现在我们来看看短信俚语，把“bt”改成“but”，“hv”改成“have”，“nt”改成“not”。但是手工制作它们是不可能的，因为它们太多了。所以我搜索了这个网站，发现了大量的俚语。

[](https://www.noslang.com/) [## 互联网俚语词典和文本俚语翻译

### 互联网俚语&缩略词词典，翻译，和其他俚语资源。让你的孩子安全:翻译他们的…

www.noslang.com](https://www.noslang.com/) 

所以让我们浏览一下网站，获取列表:

让我们理解代码

> r=http.request('GET '，' https://www . no slang . com/dictionary/'+alpha)soup = beautiful soup(r . data，' html.parser ')

这将创建一个对网页的请求，并给我们一个 HTML 页面，我们将从中提取关键字。**【alpha】**将为【a-z】，因为网页中的链接如下

对于所有以“A”开头的俚语:[https://www.noslang.com/dictionary/a](https://www.noslang.com/dictionary/a)

对于所有以“B”开头的俚语:[https://www.noslang.com/dictionary/](https://www.noslang.com/dictionary/a)B

## {'class':'dictionary-word'}是包含单词及其缩写的类

> 对于 soup.findAll('div '，{'class':'dictionary-word'})中的 I:
> 
> abbr=i.find('abbr')['title']
> 
> Abbr_dict[i.find('span ')。text[:-2]]=缩写

**【标题】**包含原话**【跨度】**包含短信俚语。我们把它保存在字典里。[:-2]因为最后两个字符对我们来说不会有任何意义。

> 对于在范围内的人(97，123):
> 
> linkDict.append(chr(one))

这将从[a-z]创建字母表，并用于追加链接

> 使用 open('ShortendText.json '，' w ')作为文件:
> 
> jsonDict=json.dump(Abbr_dict，file)

最后，我们保存字典以备后用。

> 让我们来测试一下整件事:

让我们举一个例子，看看会发生什么。这是我收到的简短推文之一

```
#Americans came in wd full support fr #India after d #PulwamaAttack , bt wait der support hs a rider.US don't want India to go on full war agnst #Pakistan ,rather they want something like #SurgicalStrike or Lil more than dt bt nt much.
```

**应用代码后，我们得到类似这样的结果:**

```
#Americans came in well done full support fr #India after d #PulwamaAttack , bit torrent wait there support headshot a rider.US do not want India to go on full war agnst #Pakistan ,rather they want something like #SurgicalStrike or Lil more than double team bit torrent nice try much.
```

**变化如下:**

```
SMS_Slangs |    what we got   |   expected____________________________________________
wd         |     well done    |   with
bt         |     bit torrent  |   but
der        |     there        |   there
hs         |     headshot     |   has 
don't      |     do not       |   do not
dt         |     double team  |   that
bt         |     bit torrent  |   but
nt         |     nice try     |   not
```

> 结论:虽然它在某些情况下表现良好，但大多数是失败的

就准确性而言:正确预测/总数

## 准确度=2/8==25%

**2。Textblob 拼写更正:**

TextBlob 是一个 python 库，提供了一个简单的 API 来访问它的方法和执行基本的 NLP 任务。

TextBlob 的一个好处是它们就像 python 字符串一样。因此，你可以像我们在 python 中那样转换和使用它。下面，我给你展示了一些基本的任务。不要担心语法，它只是让您直观地了解 TextBlob 与 Python 字符串的相关程度。

[](https://textblob.readthedocs.io/en/dev/quickstart.html#spelling-correction) [## 教程:快速入门- TextBlob 0.15.2 文档

### TextBlob 旨在通过一个熟悉的界面提供对常见文本处理操作的访问。你可以治疗…

textblob.readthedocs.io](https://textblob.readthedocs.io/en/dev/quickstart.html#spelling-correction) 

TextBlob 中有一个名为 correct()的函数可以帮助纠正拼写。让我们试试:

输出:

实际:

```
"#Americans came in wd full support fr #India after d #PulwamaAttack , bt wait der support hs a rider.US don't want India to go on full war agnst #Pakistan ,rather they want something like #SurgicalStrike or Lil more than dt bt nt much."
```

使用文本块:

```
#Americans came in we full support fr #India after d #PulwamaAttack , it wait der support he a rider.of don't want India to go on full war against #Pakistan ,rather they want something like #SurgicalStrike or Oil more than it it it much."
```

**变更如下:**

```
SMS_Slangs |    what we got   |   expected____________________________________________
wd         |     we           |   with
bt         |     it           |   but
hs         |     he           |   has
US         |     of           |   US
agnst      |     against      |   against
Lil        |     Oil          |   Little
dt         |     it           |   that
bt         |     it           |   but
nt         |     it           |   not
```

## 就准确度而言:1/9==11.11%

**3。最终 GingerGrammerCheck:**

Ginger 是一款在线拼写检查和语法错误检查工具。有一个 github Api 可以做到这一点。所以让我们也试试

[](https://github.com/Azd325/gingerit) [## Azd325/gingerit

### Python 包装器，用于根据完整句子的上下文纠正拼写和语法错误。证明…

github.com](https://github.com/Azd325/gingerit) [](https://www.gingersoftware.com/grammarcheck) [## #1 语法和拼写检查

### 生姜的世界级语法检查，一个在线工具，将纠正你犯下的任何错误。免费试用看看…

www.gingersoftware.com](https://www.gingersoftware.com/grammarcheck) 

或者您可以使用:

> pip3 安装 gingerit

> 结果:

**实际:**

```
#Americans came in wd full support fr #India after d #PulwamaAttack , bt wait der support hs a rider. US don't want India to go on full war agnst #Pakistan , rather they want something like #SurgicalStrike or Lil more than dt bt nt much.
```

**牙龈发炎后:**

```
"#Americans came in wild full support for #India after d #PulwamaAttac , but wait dear support has a rider. US don't want India to go to full war against #Pakista , rather they want something like #SurgicalStrike or Lil more than diet but not much"
```

**变化如下:**

```
SMS_Slangs |    what we got   |   expected___________________________________________
wd         |    wild          |   with
fr         |    for           |   for
bt         |    but           |   but
der        |    dear          |   there
hs         |    has           |   has
agnst      |    against       |   against
dt         |    diet          |   that
bt         |    but           |   but
nt         |    not           |   not
```

**精度方面:**

## 准确度=6/9== 66.66%

> 结论:就我们看到的所有情况而言，ginger 给了我们一个更好的结果。

# **完整代码在:**

[](https://github.com/neelindresh/PulwamaAttackAnalysis/) [## neelindresh/pulwamaattack analysis

### 通过在 GitHub 上创建一个帐户，为 neelindresh/pulwamaattackmanalogy 开发做出贡献。

github.com](https://github.com/neelindresh/PulwamaAttackAnalysis/) 

## **感谢你阅读这篇文章。**

# **如果还有其他建议，请在评论区告诉我**

[](https://dataneel.wordpress.com/) [## 面向所有人的数据科学

### 学会成长

dataneel.wordpress.com](https://dataneel.wordpress.com/) [](https://www.youtube.com/channel/UCTJE1mGfe5qgO5OfWE6surg?view_as=subscriber) [## 尼尔·巴塔查里亚

### 编程爱情

www.youtube.com](https://www.youtube.com/channel/UCTJE1mGfe5qgO5OfWE6surg?view_as=subscriber) 

更多资源。你可以点击情感分析的链接了解更多信息。

[](https://neptune.ai/blog/sentiment-analysis-python-textblob-vs-vader-vs-flair) [## Python 中的情感分析:TextBlob vs Vader 情感 vs 天赋 vs 从头构建…

### 情感分析是最广为人知的自然语言处理(NLP)任务之一。本文旨在给出…

海王星. ai](https://neptune.ai/blog/sentiment-analysis-python-textblob-vs-vader-vs-flair)