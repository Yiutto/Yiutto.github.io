---
title: Hexo+Github+Windows搭建博客（终结版）
date: 2017-11-07 15:19:34
categories: Blog
tags:   
	- git
	- markdown
	- hexo
---


# 一、前言

这是一篇使用GitHub Pages和Hexo搭建免费独立博客的总结。

作为一个数据挖掘工作者来说，虽说是个码农，但是一开始照着网上的各种教程去搭建的时候，还是云里雾里的。记得几个月前刚接触hexo（哈哈，对参加工作的我确实有点晚，没太多时间去搞），对版本控制一点概念都没有，更别说使用GitHub Page能做出一个好看又好用的博客了。所以第一次的尝试失败了。

后来的两三个月里，开始逐渐地把日常的学习都迁移到GitHub上进行，慢慢熟悉了Git的版本控制。之后，因为嫌弃csdn和博客园的样子（颜值控），最终又回归到搭建独立博客的光明大道上。

我所总结的是单纯地利用GitHub Pages搭建自己独立博客的过程，并没有额外使用独立域名之类的（谁让我现在还没工资 o(^▽^)o ）。

<!--more-->

如果是小小白，可以先花时间去了解下：

- [Git](https://git-scm.com/book/zh/v2)
- [GitHub](https://github.com/)
- [GitHub Pages](https://pages.github.com/)
- [Hexo](https://hexo.io/zh-cn/)
- [Markdown](http://www.appinn.com/markdown/#autoescape)

# 二、必要配置
## 2.1 GitHub Pages 仓库

### 2.1.1 创建对应仓库

在自己的GitHub账号下创建一个新的仓库，命名为username.github.io（username是你的账号名)。

在这里，要知道，GitHub Pages有两种类型：User/Organization Pages 和 Project Pages，而我所使用的是User Pages。

简单来说，User Pages 与 Project Pages的区别是：

1. User Pages 是用来展示用户的，而 Project Pages 是用来展示项目的。

2. 用于存放 User Pages 的仓库必须使用username.github.io的命名规则，而 Project Pages 则没有特殊的要求。

3. User Pages 将使用仓库的 master 分支，而 Project Pages 将使用 gh-pages 分支。

4. User Pages 通过 http(s)://username.github.io 进行访问，而 Projects Pages通过 http(s)://username.github.io/projectname 进行访问。


> 创建仓库，http://username.github.io；
> 创建两个分支：master 与 hexo；
> 设置hexo为默认分支（因为我们只需要手动管理这个分支上的Hexo网站文件）；

## 2.2 Git

### 2.1.1 安装Git

在windows下安装git比较常用的有两种方式：

1. [Git 官方版本的安装](https://git-scm.com/download/win)
2. [GitHub for Windows](https://desktop.github.com/)

### 2.2.2 配置Git

当安装完Git应该做的第一件事情就是设置用户名称和邮件地址。这样做很重要，因为每一个Git的提交都会使用这些信息，并且它会写入你的每一次提交中，不可更改： 

``` py
	$ git config --global user.name "username"
	$ git config --global user.email "username@example.com"
```

对于user.email，因为在GitHub的commits信息上是可见的，所以如果你不想让人知道你的email，可以Keeping your email address private:

1. 在GitHub右上方点击你的头像，选择”Settings”；
2. 在右边的”Personal settings”侧边栏选择”Emails”；
3. 选择”Keep my email address private”。

这样，你就可以使用如下格式的email进行配置：

``` py
	$ git config --global user.email "username@users.noreply.github.com"
```

## 2.3 Git 与 GitHub

### 2.3.1 git与github的区别

这里，我们要区分清楚git与github。

git是一个版本控制的工具，而github有点类似于远程仓库，用于存放用git管理的各种项目。

### 2.3.2 与github建立联系

为了能够在本地使用git管理github上的项目，需要进行一些配置，这里介绍SSH的方法。

#### 1.检查电脑是否已经有SSH KEYS

``` py
	$ ls -al ~/.ssh
	# Lists the files in your .ssh directory, if they exist
```

默认情况下，public keys的文件名是以下的格式之一：id_dsa.pub、id_ecdsa.pub、id_ed25519.pub、id_rsa.pub。因此，如果列出的文件有public和private钥匙对（例如id_ras.pub和id_rsa），证明已存在SSH keys。

#### 2.如果没有SSH KEY，则生成新的SSH KEY


``` py
	$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
	# Creates a new ssh key, using the provided email as a label
```

之后一路回车即可。

#### 3.向SSH-AGENT添加KEY

首先确保ssh-agent可运行：

``` py
    # start the ssh-agent in the background
    $ ssh-agent -s
```

然后添加SSH key：

	$ ssh-add ~/.ssh/id_rsa

#### 4.在GITHUB添加SSH KEY

首先，拷贝key：

``` py
	clip < ~/.ssh/id_rsa.pub
	# Copies the contents of the id_rsa.pub file to your cllipboard
```

然后，在GitHub右上方点击头像，选择”Settings”，在右边的”Personal settings”侧边栏选择”SSH Keys”。接着粘贴key，点击”Add key”按钮。最后，测试链接：

``` py
	$ ssh -T git@github.com
	# Attempts to ssh to GitHub
```

如果你看到：

``` py
	The authenticity of host 'github.com (207.97.227.239)' can't be established.
	RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
	Are you sure you want to continue connecting (yes/no)?
```

就键入：yes。之后将会看到如下信息：

	Hi username! You've successfully authenticated, but GitHub does not provide shell access.


**(3，4步，这里可以自行去GitHub官网手动添加)**


## 2.4 Git到本地

使用git拷贝仓库到本地主机；(username是你的GitHub用户名)

    git clone git@github.com:username/username.github.io.git

## 2.5 Hexo

在本地**username.github.io文件夹**下通过Git bash依次执行`npm install hexo`、`hexo init`、`npm install` 和 `npm install hexo-deployer-git`（此时当前分支应显示为hexo）;

### 2.5.1 安装nodejs

各个安装版本[nodejs](http://nodejs.cn/download/)

### 2.5.2 安装Hexo

#### 1.借用*npm*命令安装[Hexo](https://hexo.io/)

	npm install hexo-cli -g

运行命令发现要么出错，要么就卡死。由于是国外的东西，总是涉及翻墙问题，这里需要一个淘宝镜像,设置如下：

镜像使用方法（三种办法任意一种都能解决问题，建议使用第三种，将配置写死，下次用的时候配置还在）:

- 通过config命令

``` py
	npm config set registry https://registry.npm.taobao.org 
	npm info underscore （如果上面配置正确这个命令会有字符串response）
```

- 命令行指定

	npm --registry https://registry.npm.taobao.org info underscore 

- 编辑 ~/.npmrc 加入下面内容

	registry = https://registry.npm.taobao.org

**要是能翻墙就好了，我这里直接用**

	npm install -g hexo-cli --registry=https://registry.npm.taobao.org

#### 2.hexo init (初始化)

第一次安装时使用命令`hexo init`,会生成6个文件

 - scaffolds文件夹（文章的模板）
 - source文件夹（里面自己写的博客文件）
 - theme文件夹 （里面自己下载的主题）
 - package.json（说明使用哪些包）
 - .gitignore（限定在提交的时候哪些文件可以忽略）
 - _config.yml（相关配置文件）

**_config.yml，theme文件夹，source文件夹，这些肯定要拷贝的。**
其他的三个不是我们修改的，所以即使丢失了，也没有关系，我们可以建立一个新的文件夹，然后在里面执行`hexo init`，就会生成这三个文件，我们只需要将它们拷贝过来使用即可。

如果你在本地username.github.io文件夹下运行`hexo init`,你会发现出现了文件夹必须为空的**警告**:

``` py
	$ hexo init
	FATAL F:\Blog\Yiutto.github.io not empty, please run `hexo init` on an empty folder and then copy your files into it
	FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
	Error: target not empty
```

其实你可以把刚才git得到的**username.github.io文件夹**中的**README.md**和**.git**暂时移出去（为了保证文件夹为空）；
**如果以后移植到其他电脑上进行操作时，不需要运行`hexo init`**

#### 3.npm install (主要是根据package.json进行相应安装)

刚开始执行命令，却出现了`npm WARN deprecated swig@1.4.2: This package is no longer maintained`

后来想了一下，又是镜像源的问题，奶奶的，早知道刚开始就全局设置，算了，重新指向一下镜像源把

	npm install --registry=https://registry.npm.taobao.org

多等几下，就会出现，但是也出现类似警告（管他🐴的，安装成功就行了，之前执行`npm install`就是半天没反应）

``` py
	npm WARN deprecated swig@1.4.2: This package is no longer maintained
	npm WARN prefer global marked@0.3.6 should be installed with -g
	> hexo-util@0.6.1 postinstall F:\Blog\Yiutto.github.io\node_modules\hexo-util
	...
```


这时，你会发现多了2个东东
- node_modules
- .npmignore

#### 4.hexo g

执行命令后，你会发现又多了2个东东
- public
- db.json

#### 5.hexo s

执行后，你可以通过访问http://localhost:4000/

**总结：_config.yml，theme/，source/，scaffolds/，package.json，.gitignore，是需要拷贝的(因为这些文件才是你真正需要上传到github)；.git/，node_modules/，public/，.deploy_git/，db.json文件需要删除(`npm install`会重新生成node_modules, `hexo g`会重新生成public和db.json)**

***上面那些步骤比较针对于第一次搭建hexo。如果是后期需要在不同电脑下操作时，可在git bash中切换目录到新拷贝的文件夹里，使用 npm install 命令，进行模块安装。很明显我们这里没用hexo init初始化，因为有的文件我们已经拷贝生成过来了，所以不必用hexo init去整体初始化，如果不慎在此时用了hexo init，则站点的配置文件_config.yml里面内容会被清空使用默认值，所以这一步一定要慎重，不要用hexo init。安装其他的一些必要组件，如果在node_modules里面有的，就不要重复安装了：***

#### 6. git相关操作 (部署到GitHub上的hexo分支)

安装hexo相关git插件

	npm install hexo-deployer-git

然后依次执行`git add *` 、`git commit -m "***"` 、`git push origin hexo` 提交网站相关的文件；


`git push origin hexo`可能会出现如下错误


```
    ERROR: The key you are authenticating with has been marked as read only.
    fatal: Could not read from remote repository.
    
    Please make sure you have the correct access rights
    and the repository exists.
```

这是因为在GitHub上添加deploy key时没有打勾读写操作**Allow write access**

#### 7.执行hexo -d生成网站并部署到GitHub的master分支上

***简单地说，每个想建立GitHub Pages的仓库，起码有两个分支，一个用来存放Hexo网站的文件，一个用来发布网站。**

#### 8.hexo添加新页面（博客，Categories，Tags）

详细可见[Hexo配置官网](https://hexo.io/zh-cn/docs/index.html)

这里简单介绍一下命令

- **添加博客**

`hexo post new "MyFirstBlog"`

这时会在source\_posts文件夹下生成一个*MyFirstBlog.md*，具体设置如下

```
	---
	title: MyFirstBlog
	date: 2017-10-31 19:46:41
	categories: Blog
	tags:
	   - git
	   - markdown
	---
```

`<!--more-->`可以添加到博客中，方便前端展示“*Read More*”

- **设置tags**

`hexo page new "Tags"`
	
这时会在source\tags文件夹下生成一个*index.md*，具体如下

```
	---
	title: Tags
	date: 2017-08-11 12:12:45
	layout: "tags"
	comments: false
	---
```

- **设置categories**

`hexo page new "Categories"`

这时会在source\categories文件夹下生成一个*index.md*，具体如下

```
	---
	title: Categories
	date: 2017-08-16 15:00:44
	layout: "categories"
	comments: false
	---
```

后期需要依次执行`hexo clean`、`hexo g`、`hexo s`

# 三、个人经验总结

## 3.1 我的博客搭建流程

1. 创建仓库，Yiutto.github.io；
2. 创建两个分支：master 与 hexo；
3. 设置hexo为默认分支（因为我们只需要手动管理这个分支上的Hexo网站文件）；
4. 使用`git clone git@github.com:Yiutto/Yiutto.github.io.git`拷贝仓库；
5. 在本地Yiutto.github.io文件夹下通过Git bash依次执行`npm install hexo`、`hexo init`、`npm install` 和 `npm install hexo-deployer-git`（此时当前分支应显示为hexo）;
6. 修改_config.yml中的deploy参数，分支应为master；
7. 依次执行`git add *`、`git commit -m "***"`、`git push origin hexo`提交网站相关的文件；
8. 执行`hexo generate -d`生成网站并部署到GitHub上。

这样一来，在GitHub上的Yiutto.github.io仓库就有两个分支，一个hexo分支用来存放网站的原始文件，一个master分支用来存放生成的静态网页。完美( •̀ ω •́ )y！

## 3.2 我的博客管理流程 

### 3.2.1 日常修改

在本地对博客进行修改（添加新博文、修改样式等等）后，通过下面的流程进行管理：

1. 依次执行`git add *`、`git commit -m "***"`、`git push origin hexo`指令将改动推送到GitHub（此时当前分支应为hexo）；
2. 然后才执行`hexo generate -d`发布网站到master分支上。

虽然两个过程顺序调转一般不会有问题，不过逻辑上这样的顺序是绝对没问题的（例如突然死机要重装了，悲催….的情况，调转顺序就有问题了）。

### 3.2.2 本地资料丢失

当重装电脑之后，或者想在其他电脑上修改博客，可以使用下列步骤：

1. 使用`git clone git@github.com:Yiutto/Yiutto.github.io.git`拷贝仓库（默认分支为hexo）；
2. 在本地新拷贝的Yiutto.github.io文件夹下通过Git bash依次执行下列指令：`npm install hexo`、`npm install`、`npm install hexo-deployer-git`（记得，不需要`hexo init`这条指令）。


# 四、结尾

赶紧打赏吧，写了这么多，遇到各种坑，对了，我这在window环境下，Linux的没试过。
