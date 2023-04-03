# 如何在 Google 协同实验室中用 Python 从 ImageNet 获取图像

> 原文：<https://medium.com/coinmonks/how-to-get-images-from-imagenet-with-python-in-google-colaboratory-aeef5c1c45e5?source=collection_archive---------2----------------------->

![](img/ec23c337ebc284edf8dfcc0f432cf319.png)

计算机视觉最有趣的应用之一是图像识别，它使机器能够识别或分类它在图片上看到的东西。

神经网络目前在图像识别方面取得了最准确的结果，在一些任务上甚至在速度和准确性上超过了人类(关于这一点的更多细节在我的下一篇文章中:[https://medium . com/@ sebastiannorena/train-a-keras-neural-network-with-imagenet-synsets-in-Google-co laboratory-e 68 DC 4 FD 759 f](/@sebastiannorena/train-a-keras-neural-network-with-imagenet-synsets-in-google-colaboratory-e68dc4fd759f)

训练图像识别模型的第一步是找到属于所需类别(或多个类别)的图像，ImageNet 在这方面非常有用，因为它目前有 14，197，122 幅图像，索引了 21841 个同义词集。ImageNet 的目标是平均提供 1000 幅图像来说明 100，000 个同义词集中的每一个，大多数同义词集都是名词(80.000+)。

更多关于 ImageNet 的信息可以在这里找到:【http://www.image-net.org/about-overview 

同义词集来自 WordNet，这是一个大型的英语词汇数据库。名词、动词、形容词和副词被分成认知同义词组(同义词组)，每个同义词组表达一个不同的概念。

更多关于 WordNet 的信息可以在这里找到:【https://wordnet.princeton.edu/ 

这些有用的分类影像可使用 Python 通过以下步骤获得:

**1 收集您需要的 synsets 的 id:**

每个 synset 都有自己的 id，它被称为“WordNet ID”(wnid)。它出现在每个 synset 的 URL 末尾的“=”之后。例如，如果需要的 synset 是船只的图片，可以通过在 imagenet 网站上搜索 ship 来找到，结果将是以下页面，其 wnid 为:n04194289

[http://www.image-net.org/synset?wnid=n04194289](http://www.image-net.org/synset?wnid=n04194289)

**2 获取 synset 图像的 URL 列表:**

所述 URL 列表可以从 URL[http://www.image-net.org/api/text/imagenet.synset.geturls?下载 wnid=](http://www.image-net.org/api/text/imagenet.synset.geturls?wnid=) 后跟 wnid，因此对于船只，它将是"[http://www.image-net.org/api/text/imagenet.synset.geturls?wnid=n04194289](http://www.image-net.org/api/text/imagenet.synset.geturls?wnid=n04194289) "这可以通过 Python 库 BeautifulSoup:

```
from bs4 import BeautifulSoup
import numpy as np
import requests
import cv2
import PIL.Image
import urllibpage = requests.get("[http://www.image-net.org/api/text/imagenet.synset.geturls?wnid=n04194289](http://www.image-net.org/api/text/imagenet.synset.geturls?wnid=n04194289)")#ship synset
print(page.content)# BeautifulSoup is an HTML parsing librarysoup = BeautifulSoup(page.content, 'html.parser')#puts the content of the website into the soup variable, each url on a different line
```

自行车也是如此:

```
bikes_page = requests.get("[http://www.image-net.org/api/text/imagenet.synset.geturls?wnid=n02834778](http://www.image-net.org/api/text/imagenet.synset.geturls?wnid=n02834778)")#bicycle synset
print(bikes_page.content)# BeautifulSoup is an HTML parsing library
from bs4 import BeautifulSoup
bikes_soup = BeautifulSoup(bikes_page.content, 'html.parser')#puts the content of the website into the soup variable, each url on a different line
```

**3 分割 URL，使每个 URL 出现在不同的行上，并将它们存储在一个列表中，以便于访问:**

```
str_soup=str(soup)#convert soup to string so it can be split
type(str_soup)
split_urls=str_soup.split('\r\n')#split so each url is a different possition on a list
print(len(split_urls))#print the length of the list so you know how many urls you have
```

自行车也是如此:

```
bikes_str_soup=str(bikes_soup)#convert soup to string so it can be split
type(bikes_str_soup)
bikes_split_urls=bikes_str_soup.split('\r\n')#split so each url is a different possition on a list
print(len(bikes_split_urls))
```

**4 在谷歌协作文件系统上创建目录，以便图像可以存储在那里:**

通常会创建一个“训练”目录和一个“验证”目录来存储图像，然后使用它们来训练模型。

在本例中，有一个 ship synset 和一个 bike synset，每个目录中也有一个“ships”目录和一个“bikes”目录，如下所示:

```
!mkdir /content/train #create the Train folder
!mkdir /content/train/ships #create the ships folder
!mkdir /content/train/bikes #create the bikes folder
!mkdir /content/validation
!mkdir /content/validation/ships #create the ships folder
!mkdir /content/validation/bikes #create the bikes folder
```

**5 校正图像形状、格式，并存储在相应的目录下:**

在每个目录中存储图像的代码与其他目录是分开的，因此很容易更改每个目录的参数(图像数量、图像名称、目录、格式等)

```
img_rows, img_cols = 32, 32 #number of rows and columns to convert the images to
input_shape = (img_rows, img_cols, 3)#format to store the images (rows, columns,channels) called channels lastdef url_to_image(url):
 # download the image, convert it to a NumPy array, and then read
 # it into OpenCV format
 resp = urllib.request.urlopen(url)
 image = np.asarray(bytearray(resp.read()), dtype="uint8")
 image = cv2.imdecode(image, cv2.IMREAD_COLOR)

 # return the image
 return imagen_of_training_images=100#the number of training images to use
for progress in range(n_of_training_images):#store all the images on a directory
    # Print out progress whenever progress is a multiple of 20 so we can follow the
    # (relatively slow) progress
    if(progress%20==0):
        print(progress)
    if not split_urls[progress] == None:
      try:
        I = url_to_image(split_urls[progress])
        if (len(I.shape))==3: #check if the image has width, length and channels
          save_path = '/content/train/ships/img'+str(progress)+'.jpg'#create a name of each image
          cv2.imwrite(save_path,I)except:
        None#do the same for bikes:
for progress in range(n_of_training_images):#store all the images on a directory
    # Print out progress whenever progress is a multiple of 20 so we can follow the
    # (relatively slow) progress
    if(progress%20==0):
        print(progress)
    if not bikes_split_urls[progress] == None:
      try:
        I = url_to_image(bikes_split_urls[progress])
        if (len(I.shape))==3: #check if the image has width, length and channels
          save_path = '/content/train/bikes/img'+str(progress)+'.jpg'#create a name of each image
          cv2.imwrite(save_path,I)except:
        None

#Validation data:for progress in range(50):#store all the images on a directory
    # Print out progress whenever progress is a multiple of 20 so we can follow the
    # (relatively slow) progress
    if(progress%20==0):
        print(progress)
    if not split_urls[progress] == None:
      try:
        I = url_to_image(split_urls[n_of_training_images+progress])#get images that are different from the ones used for training
        if (len(I.shape))==3: #check if the image has width, length and channels
          save_path = '/content/validation/ships/img'+str(progress)+'.jpg'#create a name of each image
          cv2.imwrite(save_path,I)except:
        None#do the same for bikes:
for progress in range(50):#store all the images on a directory
    # Print out progress whenever progress is a multiple of 20 so we can follow the
    # (relatively slow) progress
    if(progress%20==0):
        print(progress)
    if not bikes_split_urls[progress] == None:
      try:
        I = url_to_image(bikes_split_urls[n_of_training_images+progress])#get images that are different from the ones used for training
        if (len(I.shape))==3: #check if the image has width, length and channels
          save_path = '/content/validation/bikes/img'+str(progress)+'.jpg'#create a name of each image
          cv2.imwrite(save_path,I)except:
        None

print("\nTRAIN:\n")          
print("\nlist the files inside ships directory:\n")        
!ls /content/train/ships #list the files inside ships
print("\nlist the files inside bikes directory:\n")
!ls /content/train/bikes #list the files inside bikes
print("\nVALIDATION:\n")
print("\nlist the files inside ships directory:\n")        
!ls /content/validation/ships #list the files inside ships
print("\nlist the files inside bikes directory:\n")
!ls /content/validation/bikes #list the files inside bikes
```

**结论:**本文描述了在 ImageNet 上查找所需图像、获取它们的 URL 列表、下载它们、将它们中的一些存储在一个目录(train)中(稍后可用于训练图像识别模型)以及将其他图像存储在另一个目录(validation)中(稍后可用于验证训练好的模型)所需的步骤。

目录结构是重要的，在该示例中，所使用的结构很好地用于训练 Keras 模型，因为 Keras 使用该结构来区分将用于训练的图像和将用于验证的图像，而且在每个所述目录内，Keras 使用目录名称来识别每组图像的类别。

这篇文章的所有代码也可以在谷歌合作实验室的这个链接上找到:

[](https://colab.research.google.com/drive/1MALKxRqmNdjBUXJ-6V4PFYU6inPWq7Qe) [## 谷歌联合实验室

### 编辑描述

colab.research.google.com](https://colab.research.google.com/drive/1MALKxRqmNdjBUXJ-6V4PFYU6inPWq7Qe) 

> 加入 Coinmonks [电报频道](https://t.me/coincodecap)和 [Youtube 频道](https://www.youtube.com/c/coinmonks/videos)获取每日[加密新闻](http://coincodecap.com/)

## 另外，阅读

*   [复制交易](/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c) | [加密税务软件](/coinmonks/crypto-tax-software-ed4b4810e338)
*   [网格交易](https://coincodecap.com/grid-trading) | [加密硬件钱包](/coinmonks/the-best-cryptocurrency-hardware-wallets-of-2020-e28b1c124069)
*   [密码电报信号](http://Top 4 Telegram Channels for Crypto Traders) | [密码交易机器人](/coinmonks/crypto-trading-bot-c2ffce8acb2a)
*   [最佳加密交易所](/coinmonks/crypto-exchange-dd2f9d6f3769) | [印度最佳加密交易所](/coinmonks/bitcoin-exchange-in-india-7f1fe79715c9)
*   [开发者最佳加密 API](/coinmonks/best-crypto-apis-for-developers-5efe3a597a9f)
*   最佳[密码借贷平台](/coinmonks/top-5-crypto-lending-platforms-in-2020-that-you-need-to-know-a1b675cec3fa)
*   [杠杆代币](/coinmonks/leveraged-token-3f5257808b22)终极指南
*   [7 个最佳零费用加密交易平台](https://coincodecap.com/zero-fee-crypto-exchanges)
*   [最佳网上赌场](https://coincodecap.com/best-online-casinos) | [期货交易机器人](/coinmonks/futures-trading-bots-5a282ccee3f5)
*   [分散交易所](https://coincodecap.com/what-are-decentralized-exchanges) | [比特 FIP](https://coincodecap.com/bitbns-fip)
*   用信用卡购买密码的 10 个最佳地点
*   [加拿大最佳加密交易机器人](https://coincodecap.com/5-best-crypto-trading-bots-in-canada) | [Bybit vs 币安](https://coincodecap.com/bybit-binance-moonxbt)