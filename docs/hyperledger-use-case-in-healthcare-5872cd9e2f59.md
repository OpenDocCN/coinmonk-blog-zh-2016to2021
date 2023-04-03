# 医疗保健中的 Hyperledger 用例

> 原文：<https://medium.com/coinmonks/hyperledger-use-case-in-healthcare-5872cd9e2f59?source=collection_archive---------1----------------------->

在本文中，我们将了解区块链如何帮助医疗保健行业，在该行业中，临床试验可以比传统和复杂的数据流好得多。

**临床试验**是在[临床研究](https://en.wikipedia.org/wiki/Clinical_research)中完成的[实验](https://en.wikipedia.org/wiki/Experiment)或[观察](https://en.wikipedia.org/wiki/Observation)。此类针对[人类参与者](https://en.wikipedia.org/wiki/Human_subject_research)的[前瞻性](https://en.wikipedia.org/wiki/Prospective_cohort_study)生物医学或行为研究旨在回答关于生物医学或行为干预的具体问题，包括新的[治疗](https://en.wikipedia.org/wiki/Therapy)(如新型[疫苗](https://en.wikipedia.org/wiki/Vaccine)、[药物](https://en.wikipedia.org/wiki/Pharmaceutical_drug)、[饮食选择](https://en.wikipedia.org/wiki/Medical_nutrition_therapy)、[膳食补充剂](https://en.wikipedia.org/wiki/Dietary_supplement)和[医疗器械](https://en.wikipedia.org/wiki/Medical_device))以及需要进一步研究和比较的已知干预。(来源维基)

读了这些，很明显临床试验必须在严密的监控下进行，并且他们执行或采取的每一个行动都应该被记录和防篡改。许多现有技术可以更好地执行这一任务，但在“区块链”发展之后。医疗行业已经开始在满足数据安全性、完整性和高可用性的每个阶段采用这种方法。

我们将在第 4 篇文章中看到这个用例，到第 4 篇文章结束时，你应该能够使用 Hyperledger 建立一个区块链网络，编写链码并创建 rest-API 来访问它。

不幸的是，我不是前端开发人员，所以我不能承诺前端设计，但我会确保完整的后端启动和运行没有任何缺陷。！

不要再说了！，让我们进入正题..

## **要完成的步骤**

*   先决条件—环境准备
*   Hyperledger 网络设置
*   链码设置—使用案例
*   REST-API 设置——读写区块链

**步骤 1 —总账网络设置—环境准备**

```
# Exit on any failureset -e# Array of supported versionsdeclare -a versions=('trusty' 'xenial' 'yakkety', 'bionic')# check the version and extract codename of ubuntu if release codename not provided by userif [ -z "$1" ]; then source /etc/lsb-release || \ (echo "Error: Release information not found, run script    passing Ubuntu version codename as a parameter"; exit 1) CODENAME=${DISTRIB_CODENAME}else CODENAME=${1}fi
```

**更新套餐列表**

```
# Updating package listssudo apt-add-repository -y ppa:git-core/ppasudo apt-get update
```

**安装 Git**

```
echo "# Installing Git"sudo apt-get install -y git
```

**安装 NVM**

```
# Execute nvm installation scriptecho "# Executing nvm installation script"curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
```

**在不重启外壳的情况下设置 npm 环境**

```
export NVM_DIR="${HOME}/.nvm"[ -s "${NVM_DIR}/nvm.sh" ] && . "${NVM_DIR}/nvm.sh"[ -s "${NVM_DIR}/bash_completion" ] && . "${NVM_DIR}/bash_completion"
```

**安装节点**

```
echo "# Installing nodeJS"nvm install 8.16.1nvm use 8.16.1
```

**确保安装了 CA 证书**

```
sudo apt-get -y install apt-transport-https ca-certificates
```

**将 Docker 存储库密钥添加到 APT 钥匙串**

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

**#更新 APT 搜索 Docker 包的位置**

```
echo “deb [arch=amd64] [https://download.docker.com/linux/ubuntu](https://download.docker.com/linux/ubuntu) ${CODENAME} stable” | \sudo tee /etc/apt/sources.list.d/docker.list
```

**更新包列表**

```
sudo apt-get update
```

**验证 APT 正在从正确的存储库中提取**

```
sudo apt-cache policy docker-ce
```

**安装内核包，允许我们在 V14(可信/自动)下使用 aufs 存储驱动程序**

```
if [ "${CODENAME}" == "trusty" ]; then echo "# Installing required kernel packages" sudo apt-get -y install linux-image-extra-$(uname -r) linux-image-extra-virtualfi
```

**安装码头工人**

```
echo "# Installing Docker"sudo apt-get -y install docker-ce
```

**将用户帐户添加到 docker 组**

```
sudo usermod -aG docker $(whoami)
```

**#安装 docker 撰写**

```
echo "# Installing Docker-Compose"sudo curl -L "https://github.com/docker/compose/releases/download/1.13.0/docker-compose-$(uname -s)-$(uname -m)" \-o /usr/local/bin/docker-composesudo chmod +x /usr/local/bin/docker-compose
```

**如果需要，安装 python v2**

```
set +eCOUNT="$(python -V 2>&1 | grep -c 2.)"if [ ${COUNT} -ne 1 ]then sudo apt-get install -y python-minimalfi
```

**安装安装 hyperledger fabric 所需的 unzip。**

```
sudo apt-get -y install unzipsudo apt-get -y install zip
```

**安装 Nginx**

```
echo "# Installing Nginx"sudo apt install nginx
```

**#安装 mongodb**

```
echo "# Installing mongodb"sudo apt install -y mongodbsudo apt-get install jq
```

**#安装 Go &安装路径**

```
1) sudo apt install -y golang
2) export GOROOT=/usr/lib/golang
3) export GOPATH=$HOME/projects
4) export PATH=$PATH:$GOROOT/bin
5) Set this in .profile file for permenantly
6) . ~/.profile -> this command will help you to refresh the profile without loggingout
```

**#安装布料**

```
------curl **-**sSL http:**//**bit**.**ly**/**2ysbOFE **|** bash **-**s **<**fabric**>** **<**fabric**-**ca**>** **<**thirdparty**>----**1) curl **-**sSL http:**//**bit**.**ly**/**2ysbOFE **|** bash **-**s 1.4.4 1.4.4 0.4.18So if you want 1.4.0 version, change that accordingly in above command
```

这将需要一些时间来下载 Fabric 的图像&一旦完成。你应该可以看到织物样本文件夹。

如果您能够没有任何问题地设置这个环境，那么您就准备好进入下一篇文章了。

让我们在下一篇文章中继续讨论。

> [在您的收件箱中直接获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)

[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)