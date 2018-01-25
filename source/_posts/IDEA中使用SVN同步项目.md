---
title: IDEA中使用SVN同步项目
photos:
  - /gallery/idea.jpg
date: 2017-11-24 15:52:58
categories: Scala
tags:
  - IDEA
---

以前都用git同步项目，应公司需要，所用项目必须使用svn上传公司服务器，这里简单介绍一些。今天尝试配置了一下idea的svn插件，其实也没有想像中的那么麻烦。我使用的idea是13.0.4版本，这个版本中支持了最新的Subversion 1.8；相对于之前版本，Subversion 1.8提供了SVN最新的使用方法 -- 客户端命令行来代替SVNKit，这种方法更灵活，使对未来版本的支持更加容易。
这里有这个问题的[官方说明](http://blog.jetbrains.com/idea/2013/12/subversion-1-8-and-intellij-idea-13/)

<!--more-->
# 1.SVN
既然需要使用客户端命令行，那我们就需要在机器上安装一个SVN客户端命令行程序，可以到这里下载对应的安装[程序](http://subversion.apache.org/packages.html#windows)

![svn](/gallery/IntelliJ-IDEA-Maven-Scala/svn1.png)

我选择的是**SlikSVN**，因为其它的好像都是客户端和服务器安装程序，只有它仅包含SVN客户端。安装成功后配置一下环境变量PATH（其实不配也没关系，对idea来说没关系，仅在使用命令行时需要），配置好环境变量后测试一下：
```
	C:\Users\Administrator>svn --version --quiet  
	1.8.10-SlikSvn-1.8.10-X64
```

# 2.IDEA使用SVN

1. 启用idea的版本控制插件（这里当然是启用Subversion），打开`VCS`菜单项然后点击`enable Version Control Integration...`菜单

![svn](/gallery/IntelliJ-IDEA-Maven-Scala/svn3.png)

2. 选择`Subversion`项：

![svn](/gallery/IntelliJ-IDEA-Maven-Scala/svn4.png)

3. 再到`checkout`菜单去操作就OK了，不然总是报错，连浏览svn服务器上的内容都无法做到

![svn](/gallery/IntelliJ-IDEA-Maven-Scala/svn5.png)

想启用idea的SVN插件还需要在idea配置一下（Ctrl + Alt + S），如下图所示：

![svn](/gallery/IntelliJ-IDEA-Maven-Scala/svn2.png)

好像IDEA自带了svn，当然你也可以配置自己刚才安装的。

# 3.IDEA项目中如何使用SVN

## 3.1 更新、提交和显示历史版本

右击`文件/文件夹/项目`，`Update File`更新文件，`Commit File`提交文件，`Compare with Latest Repository Version`与远程仓库中最新的版本进行对比，`Show History`显示提交历史

![svn](/gallery/IntelliJ-IDEA-Maven-Scala/svn6.png)

## 3.2 冲突解决

但更新时Idea发现有人修改了同一文件的同一行代码时，需要手动解决冲突。

 - **Accept Yours：**使用你本地的代码，覆盖远程仓库中的更新

 - **Accept Theirs：**使用远程仓库中的代码，覆盖本地修改

 - **Merge：**进入冲突解决界面
 
 ![svn](/gallery/IntelliJ-IDEA-Maven-Scala/svn7.png)
 
点击`Merge`后进入如下界面，左侧为本地代码，右侧为远程仓库代码，中间为合并后的结果,点击`Apply`确定合并代码。

 ![svn](/gallery/IntelliJ-IDEA-Maven-Scala/svn8.png)


# Refrence
http://www.cnblogs.com/zhanghaoliang/p/6206948.html
http://blog.csdn.net/qq_27093465/article/details/51580290
 