---
title: IDEA搭建Maven和配置pom文件
photos:
  - /gallery/idea.jpg
date: 2017-11-24 14:47:03
categories: Scala
tags:
  - scala
  - maven
  - IDEA
---

前面介绍了如何利用IntelliJ-IDEA与Maven学习Scala编程，今天来介绍一下Maven的搭建，pom.xml文件的认识。
<!--more-->

# 1.Idea开发环境中搭建Maven

当配置完Maven之后，我们需要给Idea配置Maven，那么首先必须先要安装Idea，Idea的安装在这里就不累赘了，请看我之前的博文，非常简单，下一步下一步即可，安装完成之后打开Idea设置Maven，单击`Setting`之后，设置`Maven`节点下的`Maven home directory`和`user settings file`和`local repository`如图所示：

![Maven](/gallery/IntelliJ-IDEA-Maven-Scala/maven1.png)

其中`user settings file`和`local repository`这2个文件是和pom.xml文件绑在一起的，里面都是工程需要的jar包，一般来说在新建工程时我们考虑到下载了所有的jar，当然你也可以从其他电脑那拷贝过来。

# 2.MAVEN自动导入jar包
这里以spark项目为例，比如我们需要在scala文件导入，eg：
``` Scala
	import org.apache.spark.mllib.tree.model.GradientBoostedTreesModel
	import org.apache.spark.rdd.RDD
	import org.apache.spark.sql.{DataFrame, SaveMode, SparkSession}
```
我们需要的包有`spark.sql`和`spark.mllib`等，一般文件导入包是根据`pom.xml`配置文件，我们需要在`pom.xml`写入如下代码

``` Scala
	<!--spark-->
	<dependency>
	  <groupId>org.apache.spark</groupId>
	  <artifactId>spark-core_2.11</artifactId>
	  <version>2.0.2</version>
	</dependency>
	<dependency>
	  <groupId>org.apache.spark</groupId>
	  <artifactId>spark-sql_2.11</artifactId>
	  <version>2.0.2</version>
	</dependency>
	<dependency>
	  <groupId>org.apache.spark</groupId>
	  <artifactId>spark-streaming_2.11</artifactId>
	  <version>2.0.2</version>
	</dependency>
	<dependency>
	  <groupId>org.apache.spark</groupId>
	  <artifactId>spark-mllib_2.11</artifactId>
	  <version>2.0.2</version>
	</dependency>
```

打开[Maven Repository](http://mvnrepository.com/),搜索spark，进入如下界面

![spark](/gallery/IntelliJ-IDEA-Maven-Scala/maven2.png) 

点击** Spark Project ML Library**，进入如下界面，根据选择Scala版本选择自己的版本

![mllib](/gallery/IntelliJ-IDEA-Maven-Scala/maven3.png)

这里我根据我的Scala包选择,将里面的代码复制到`pom.xml`，然后右击`Maven` -> `Reimport`。(耐心的等把)这样，Maven就会开始自动下载jar包到本地仓库，然后关联到你的项目中。如下图

![mllib](/gallery/IntelliJ-IDEA-Maven-Scala/maven4.png)
![mllib](/gallery/IntelliJ-IDEA-Maven-Scala/maven5.png)


下载完成后，查看External Libraries，会不会发现，瞬间导入了好多jar包（当然不是瞬间，这得看你的网速了）呢。

这就是Maven的强大之处，如果你需要使用spark开发大数据项目的话，只需记住几个重要的包的名字，就可以轻松将所有包导入项目中。

# 注意：**maven的local repository目录问题

默认会放在`~/.m2/repository`目录下 （“~”代表用户的目录，安装Maven后我们会在用户目录下发现`.m2` 文件夹。默认情况下，该文件夹下放置了Maven本地仓库`.m2/repository`。所有的Maven构件(artifact)都被存储到该仓库中，以方便重用)。
*有的时候方便，我们通常直接从同事的电脑里面移植repository过来。*