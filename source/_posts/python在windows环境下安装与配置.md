---
title: python在windows环境下安装与配置
photos:
  - /gallery/python.png
date: 2017-12-02 17:30:29
categories: Python
tags: 
  - python
  - pip
  - ipython
---

学习数据分析时，对python和R语言都有研究过，后来就一条心学习python，不要问我为什么。闲时之际，写下一些配置python的环境和方法，作为笔记，方便过后学习安装。年纪大了记不住东西哈。
<!--more-->

# 1.python版本简单介绍
 - [Anaconda](https://www.anaconda.com/download/)
 - [python官网](https://www.python.org/)
 - [pycharm](www.jetbrains.com)
 
如果简单地学习python的话，下载python官网即可；如果做学术研究的话，可以下载Anaconda（里面有好多科学计算包，而且有针对学生特有的科学计算开源包，用一个教育邮箱即可获取）；如果工作中，建议使用pycharm。

**个人：**上面三个我都用过，简单总结一下，Anaconda版本开源计算包虽多，但好多其实用不到，我们无非用的就是[机器学习](http://scikit-learn.org/)和[深度学习](https://www.tensorflow.org/)，**numpy**，**pandas**等。而且Anaconda比较耗内存，界面和RStudio一样。后期工作中，用了JETBRAINS的IDEA，界面性能上比较符合用户习惯，而后使用pycharm作为编程工具。

# 2.python的pip，ipython（python2和3共用）
学习过程中，经常会遇到python2和python3的转化，有的人使用virtualenv。在这我仅介绍python在windows下版本共用。[python官网](https://www.python.org/)可以下载到python2.7和python3.5。

## 2.1 python2和python3
 如果常用python2，建议就将python2的path放在最前面，建议将`python.exe`的名称改`python2.exe`，python3也是如此，这样就可以在cmd中直接使用两个不同的python了(好像python2和python3的安装路径一般独有2个文件（`python.exe`和`python2.exe`）或（`python.exe`和`python3.exe`），为了避免调用python冲突，可以删除`python.exe`)，如下：

 ![python](/gallery/python-windows/python1.png)
 

## 2.2 pip2和pip3
```
	C:\Users\Administrator>pip2
	Fatal error in launcher: Unable to create process using '"'
	C:\Users\Administrator>pip3
	Fatal error in launcher: Unable to create process using '"'
```
 ![python](/gallery/python-windows/python2.png)

这是因为刚开始没有想过同时将两个环境都配置为可用，所以当需要*python2*的时候，将*path*优先设置为*python2*，然后直接安装的依赖工具。当需要*python3*的时候，将*path*优先设置为*python3*，然后直接安装的依赖工具。所以当工具都以*python名称*安装完成之后，现在又将*python名称*修改为*python2*或*python3*导致的。如下图:

![python](/gallery/python-windows/python3.jpg)

解决的办法就是将pip.exe或ipython.exe等工具使用文本的方式打开，找到python安装目录，然后修改相应的名称，最后保存即可。
![python](/gallery/python-windows/python4.png)
![python](/gallery/python-windows/python5.png)

# 3.python的pip镜像源设置

有的时候用`pip install`安装package时，总是网速很慢或者HTTP Response Outtime。

## 3.1 国内源

清华：https://pypi.tuna.tsinghua.edu.cn/simple

阿里云：http://mirrors.aliyun.com/pypi/simple/

中国科技大学: https://pypi.mirrors.ustc.edu.cn/simple/

华中理工大学：http://pypi.hustunique.com/

山东理工大学：http://pypi.sdutlinux.org/ 

豆瓣：http://pypi.douban.com/simple/

## 3.2 临时使用

可以在使用`pip`的时候加参数`-i https://pypi.tuna.tsinghua.edu.cn/simple`

例如：`pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pyspider`，这样就会从清华这边的镜像去安装pyspider库。

## 3.1 永久修改，一劳永逸

Linux下，修改`~/.pip/pip.conf` (没有就创建一个文件夹及文件。文件夹要加“.”，表示是隐藏文件夹)

内容如下：

```
	[global]
	index-url = https://pypi.tuna.tsinghua.edu.cn/simple
	[install]
	trusted-host=mirrors.aliyun.com
```
windows下，直接在user目录中创建一个pip目录，如：`C:\Users\xx\pip`，新建文件`pip.ini`。内容同上。

#4 个人找到的比较全site-package网站（当用pip安装出现Unable to find vcvarsall.bat）

现在python2好多package找不到了，大家可以尝试在这个网站
[Unofficial Windows Binaries for Python Extension Packages](https://www.lfd.uci.edu/~gohlke/pythonlibs/)找一下，大部分能找到。下载下来的是`.whl`文件，找到该文件的路径，用`pip install .whl文件名`安装即可。
 
