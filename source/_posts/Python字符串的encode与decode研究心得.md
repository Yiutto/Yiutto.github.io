---
title: Python字符串的encode与decode研究心得
photos:
  - /gallery/python.png
date: 2018-01-25 10:31:26
categories: Python
tags:
  - python
---
为什么Python使用过程中会出现各式各样的乱码问题，明明是中文字符却显示成“/xe4/xb8/xad/xe6/x96/x87”的形式？为什么会报错**“UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)”？**本文就来研究一下这个问题。
<!--more-->

# 理解decode和encode

字符串在Python内部的表示是`unicode`编码，因此，在做编码转换时，通常需要以`unicode`作为中间编码，即先将其他编码的字符串解码（decode）成unicode，再从unicode编码（encode）成另一种编码。 
> `decode`的作用是将其他编码的字符串转换成unicode编码，如str1.decode('gb2312')，表示将gb2312编码的字符串str1转换成unicode编码。 
> `encode`的作用是将unicode编码转换成其他编码的字符串，如str2.encode('gb2312')，表示将unicode编码的字符串str2转换成gb2312编码。
 
**因此，转码的时候一定要先搞明白，字符串str是什么编码，然后decode成unicode，然后再encode成其他编码**


# 代码中字符串的默认编码与代码文件本身的编码一致。 

-      如：`s='中文'`如果是在`utf8`的文件中，该字符串就是`utf8`编码，如果是在`gb2312`的文件中，则其编码为`gb2312`。这种情况下，要进行编码转换，都需要先用`decode`方法将其转换成`unicode`编码，再使用`encode`方法将其转换成其他编码。通常，在没有指定特定的编码方式时，都是使用的系统默认编码创建的代码文件。 

- 如果字符串是这样定义：`s=u'中文'`则该字符串的编码就被指定为`unicode`了，即`python`的内部编码，而与代码文件本身的编码无关。因此，对于这种情况做编码转换，只需要直接使用encode方法将其转换成指定编码即可。


- 如果一个字符串已经是`unicode`了，再进行解码则将出错，因此通常要对其编码方式是否为unicode进行判断：

``` python
	isinstance(s, unicode)  #用来判断是否为unicode 
```

**用非unicode编码形式的str来encode会报错** 

# 如何获得系统的默认编码？

``` python 
	#!/usr/bin/env python
	#coding=utf-8
	import sys
	print sys.getdefaultencoding() 
```
	
该段程序在英文WindowsXP上输出为：`ascii` 
在某些IDE中，字符串的输出总是出现乱码，甚至错误，其实是由于IDE的结果输出控制台自身不能显示字符串的编码，而不是程序本身的问题。 

如在UliPad中运行如下代码：
``` python 
	s=u"中文"
	print s 
```

会提示：**UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)**。这是因为UliPad在英文WindowsXP上的控制台信息输出窗口是按照ascii编码输出的（英文系统的默认编码是ascii），而上面代码中的字符串是Unicode编码的，所以输出时产生了错误。

将最后一句改为：`print s.encode('gb2312')`, 则能正确输出`“中文”`两个字。
若最后一句改为：`print s.encode('utf8')` 则输出：`/xe4/xb8/xad/xe6/x96/x87`，这是控制台信息输出窗口按照`ascii`编码输出`utf8`编码的字符串的结果。

`unicode(str,'gb2312')`与`str.decode('gb2312')`是一样的，都是将`gb2312`编码的`str`转为`unicode`编码，使用`str.__class__`可以查看str的编码形式

原理说了半天，最后来个包治百病的吧：）

``` python
	#!/usr/bin/env python  
	#coding=utf-8  
	s="中文"  
  
	if isinstance(s, unicode):  
		#s=u"中文"  
		print s.encode('gb2312')  
	else:  
		#s="中文"  
		print s.decode('utf-8').encode('gb2312') 
```

# 工作中遇到的'UTF8' CODEC CAN'T DECODE BYTE 0XD1 IN POSITION 931: INVALID CONTINUATION BYTE解决方法

有时候，我得到这样的字符œ导致的UnicodeDecodeError错误。我需要能够使串的UTF-8有或没有这些字符。

在工作中，经常遇到，读取一个文件，或者是从网页获取一个问题，明明看着是gb2312的编码，可是当使用decode转时，总是出错，这个时候，可以使用decode(‘gb18030′)这个字符集来解决，如果还是有问题，这个时候，一定要注意，decode还有一个参数，比如，若要将某个 String对象s从gbk内码转换为UTF-8，可以如下操作

```
	s.decode(‘gbk’).encode(‘utf-8′)
```

可是，在实际开发中，我发现，这种办法经常会出现异常：**UnicodeDecodeError: ‘gbk’ codec can’t decode bytes in position 30664-30665: illegal multibyte sequence**。这是因为遇到了非法字符——尤其是在某些用C/C++编写的程序中，全角空格往往有多种不同的实现方式，比如\xa3\xa0，或者\xa4\x57，这些字符，看起来都是全角空格，但它们并不是“合法”的全角空格（真正的全角空格是\xa1\xa1），因此在转码的过程中出现了异常。这样的问题很让人头疼，因为只要字符串中出现了一个非法字符，整个字符串——有时候，就是整篇文章——就都无法转码。

解决办法：

```
	s.decode(‘gbk’, ‘ignore’).encode(‘utf-8′)
```

因为decode的函数原型是`decode([encoding], [errors='strict'])`，可以用第二个参数控制错误处理的策略，默认的参数就是`strict`，代表遇到非法字符时抛出异常；

- 如果设置为ignore，则会忽略非法字符；
- 如果设置为replace，则会用?取代非法字符；
- 如果设置为xmlcharrefreplace，则使用XML的字符引用。