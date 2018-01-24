---
title: IDEA如何将项目打包至jar放至Linux系统下
photos:
  - /gallery/idea.jpg
date: 2017-11-24 16:09:56
categories: Scala
tags:
  - scala
  - maven
  - IDEA
---

新建好的maven工程，为了方便在Linux环境中运行，将其打包成jar格式，用`spark-submit`命令放在spark集群环境下运行，为了方便后期忘事，作此笔记

<!--more-->

# IDEA将maven工程打包

单击`File`->`Project Structure`->`Artifacts`，

![Jar打包](/gallery/scala-jar/jar1.png)
![Jar打包](/gallery/scala-jar/jar2.png)

选择自己需要打包的main函数，

![Jar打包](/gallery/scala-jar/jar3.png)
![Jar打包](/gallery/scala-jar/jar4.png)

这里以`getLineMain`为例，上图中箭头1表示的是主函数的类，这个后期在linux环境中运行需要用到。箭头2中的路径最好重新配置一下，其中`META-INF/MANIFEST.MF`的文件路径最好设置在`src`路径下: `...\src\MF-IDF\jar的名字`

![Jar打包](/gallery/scala-jar/jar5.png)

点击OK后，继续配置相关路径
![Jar打包](/gallery/scala-jar/jar6.png)

将上图箭头1，2，3的名字重新命名，具体如下图。**jar的名字一定要统一**
![Jar打包](/gallery/scala-jar/jar7.png)

点击ok，你会在工程的目录下看见生成了一个刚刚设置路径的文件夹`MF-INF`
![Jar打包](/gallery/scala-jar/jar8.png)

别以为这样就完了，点击`Build`->`Build Artifacts...`，选好自己的jar包
![Jar打包](/gallery/scala-jar/jar9.png)

在`out`文件夹下可以发现jar文件
![Jar打包](/gallery/scala-jar/jar10.png)

# 将jar文件放置Linux的spark集群上

此时你需要一个sh脚本，方便后台运行

``` Linux
	zip -d getBaseLine.jar META-INF/*.RSA META-INF/*.DSA META-INF/*.SF
	nohup spark-submit --class trafficLine.getLineMain getBaseLine.jar >> /home/spark/DataminingServer/logs/yiutto/Yt_ntProcess.log 2>&1 &
```

`nohup`为LINUX命令用法，不挂断地运行命令，要运行后台中的 `nohup` 命令，添加 `& `( 表示"and"的符号)到命令的尾部。
> 1.查看运行的后台进程`jobs -l`，jobs命令只看当前终端生效的，关闭终端后，在另一个终端jobs已经无法看到后台跑得程序了，此时利用ps（进程查看命令）
> 2.`ps -ef`, 用`ps -def | grep`查找进程很方便，注：用`ps -def | grep`查找进程很方便，最后一行总是会grep自己，用grep -v参数可以将grep命令排除掉 
> 3.再用awk提取进程ID，`ps -aux|grep chat.js| grep -v grep | awk '{print $2}'`


[nohup和&后台运行，进程查看及终止](http://www.cnblogs.com/baby123/p/6477429.html)

`/home/spark/DataminingServer/logs/yiutto/Yt_ntProcess.log`是我的日志路径
