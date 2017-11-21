---
title: Spark之Scala Studying
date: 2017-11-18 10:38:05
categories: Scala
tags:
	- scala
photos:
   - /gallery/scala.jpg
---

# 一、前言

这是一篇使用Scala的一些总结。

作为大数据来说，学习好spark这是必备技能，要想更容易得用spark，必须学会Scala基本语法，spark就是用Scala写的。

Scala它以JVM为目标环境，将面向对象和函数式编程有机地结合在一起。结合快学Scala作的自我总结，方便后期自己阅读。

<!--more-->

# 二、相关介绍

版本：Spark version 2.0.2	Scala version 2.11.7 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_121)

## 1.基础

### 1.1 声明变量

val 和 var之间的区别：val定义常量，var定义变量，必须给出变量的类型

eg：` val String a = “yiutto” `

### 1.2 Scala Doc

你可以在[www.scala-lang.org/api](http://www.scala-lang.org/api)在线浏览Scaladoc。

## 2.控制结构和函数

### 2.1 条件表达式

if/else , 可以把()当做是表示“无有用值”的占位符，将Unit当做Java或C/C++中的void。
eg：

``` Scala
	if (x > 0) { 1
	} else if (x == 0) { 0
	} else -1
```

从技术上讲，void没有值但是Unit有一个表示“无值”的值。如果你一定要深究的话，这就好比空的钱包和里面有一张写着“没钱”的无面值钞票的钱包之间的区别。

**说明：**Scala没有switch语句，不过它有一个强大很多的模式匹配机制。

### 2.2 循环

Scala拥有与Java和C++相同的while和do循环。

**说明：**在for循环的变量之前并没有val或var的指定。Scala并没有提供break或continue语句来退出循环。那么如果需要break时我们该怎么做？

1.使用Boolean型的控制变量。
2.使用嵌套函数--你可以从函数当中return。
3.使用Breaks对象中的break方法：

``` Scala
	import scala.util.control.Breaks._
	breakable {
	  for (...) {
	    if (...) break; // 退出breakable块
		...
	  }
	}
```

在这里，控制权的转移是通过抛出和捕获异常完成的，因此，如果时间很重要的话，你应该尽量避免使用这套机制。

**高级for循环和推导式**

`for (i <- 1 to 3; j <- 1 to 3 if i != j) println((10 * i + j) + " ")// 将打印 12 13 21 23 31 32`

`for ( i <- 1 to 10) yield i % 3  // 生成Vector（1，2，0，1，2，0，1，2，0，1）`

### 2.3 函数

`def abs(x: Double) = if ( x >= 0) x else -x`

**提示：**虽然在带名函数中使用return并没有什么不对，我们最好适应没有return的日子。很快，你就会使用大量匿名函数，这些函数中return并不返回值给调用者。它跳出到包含它的带名函数中。我们可以把return当做是函数版的break语句，仅在需要时使用。

**变长参数**


## 4.映射与元祖

### 4.1 映射

不可变映射，eg：`val scores = Map("Alice" -> 10)`，其值不能被改变。

可变映射，eg：`val scores = scala.collection.mutable.Map("Alice" -> 10)`

定义一个空的映射，eg：`val scores = new scala.collection.mutable.HashMap[String, Int]`

### 4.2 取映射中的值

eg1：`val bobScore = scores("Bob") //类似于Java中的scores.get("Bob")`
eg2：`val bobScore = socres.getOrElse("Bob", 0) //如果映射包含键”Bob“，返回对应的值；否则，返回0`

最后，映射.get(键)这样的调用返回一个Option对象，要么是Some（键对应的值），要么是None。

### 4.3 更新映射中的值

eg1：`scores("Bob") = 0`

eg2: `scores += ("Bob" -> 10, "Fred" -> 7) //添加新的键-值对，假设映射是可变的`

你不能更新一个不可变的映射，但你可以做一些同样有用的操作----获取一个包含所需要的更新的新映射

eg3：`val newScores = scores + ("Bob" -> 10, "Fred" -> 7) //用新的变量重新赋值

除了把结果作为新值保存外，你也可以更新var变量：

```
	var scores = ...
	scores = scores + ("Bob" -> 10, "Fred" -> 7)
```

同理，移除某个键，可以用”-“操作符，eg：` scores = scores - "Alice" `

### 4.4 已排序映射

eg1: 
``` Scala
	val scores = scala.collection.immutable.SortedMap("Alice" -> 10, "Fred" ->7, "Bob" -> 3, "Cindy" -> 8) 
	//默认升序`
```

eg2:
``` Scala 
	val months = scala.collection.mutable.LinkedHashMap("Alice" -> 10, "Fred" ->7, "Bob" -> 3）
	//按插入顺序访问所有键，使用LinkedHashMap `
```

### 4.5 元组

映射是键/值对偶的集合。对偶是元组的最简单形态
eg1：
```
	val t = (1, 3.15, "Fred")
	val (first, second, third) = t
	val (first, second, _) = t
```

元组可以用于函数需要返回不止一个值的情况。举例来说，StringOps的partition方法返回的是一对字符串，分别包含了满足某个条件和不满足该条件的字符：

eg2：` "New York".partition(_.isUpper) //输出对偶（“NY”，“ew ork”）

### 4.6 拉链操作

使用元组就是为了把多个值绑在一起，通常用zip方法完成。

eg：
```
	val symbols = Array("<", "-", ">")
	val counts = Array(2, 10, 1)
	val pairs = symbols.zip(counts)
	输出Array(("<", 2), ("-", 10), (">", 1))
```

## 5.类

### 5.1 简单类和无参方法

``` Scala
	class Counter {
	  private var value = 0 //你必须初始化字段
	  def increment() { //方法默认是公有的
	    value += 1
	  }
	  def current() = value
	}
```

使用该类需要做的就是构造对象并按照通常的方式来调用方法：

``` Scala
	val myCounter = new Counter()
	myCounter.increment()
	println(myCounter.current)
```

调用无参数方法（比如current），可以不用写圆括号；一般来说，对于**改值器**方法（即改变对象状态的方法，eg：increment()）使用()，面对于**取值器**方法（不会改变对象状态，eg：current()）去掉()是不错的习惯风格。

### 5.2 带getter和setter的属性

Scala对每个字段都提供getter和setter方法。在这里，我们定义一个字段：
``` Scala
	class Person {
	  var age = 0
	}
```

Scala生成面向JVM的类，其中有一个私有的**age**字段以及对应的**getter**和**setter**方法。在这里这两个方法是公有的，因为我们没有将age声明为private。注意这里是用**var**；如果是**val**，**setter**方法是不能用的（对私有字段而言，getter和setter方法也是私有的）。

在Scala中，**getter和setter**分别叫做**age**和**age_=**。eg：

``` Scala
	println(fred.age) //调用了fred.age()方法也是私有的）。
	fred.age = 21 //调用了fred.age_=(21)
```

**说明：**在Scala中，getter和setter方法并非被命名为getXxx和setXxx，不过它们的意义是一样的。在任何时候你都可以自己重新定义getter和setter方法。eg：

``` Scala
	class Person {
	  private var privateAge = 0 //变为私有并改名
	  
	  def age = privateAge
	  def age_=(newValue: Int) {
	    if (newValue > privateAge) privateAge = newValue; //不能变年轻
	  }
	}
```

这里面虽然**privateAge**是私有的，但是**age**和**age_=**被重新定义（公有的），所以以下代码是可运行的。eg：

``` Scala
	val fred = new Person
	fre.age = 30
	fre.age = 22
	println(fred.age) //30
```

**提示：**Scala对每个字段生成getter和setter方法听上去有点恐怖，不过你可以控制这个过程。
 - 如果字段是私有的，则getter和setter方法也是私有的。（除非自己重新定义为公有的）
 - 如果字段是val，则只有getter方法被生成。
 - 如果不需要任何getter和setter，可以将字段声明为private[this]。

### 5.3 只带getter的属性

有时候你只需要一个**只读**属性，有*getter*但没有*setter*。如果属性的值在对象构建完成后就不再改变，则可以使用val字段：

``` Scala
	class Message {
	  val timeStamp = new java.util.Date
	  ...
	}
```

Scala会生成一个私有的final字段和一个getter方法，但没有setter。不过，有时候你需要这样一个属性，客户端不能随意改值，但它可以通过某种其他的方式被改变。5.1节中的Counter类就是个很好的例子。从概念上讲，counter有一个current属性，当increment方法被调用时更新，但并没有对应的setter。你不能通过val来实现这样一个属性——val永不改变。你需要提供一个私有字段和一个属性的getter方法，eg：

``` Scala
	class Counter {
	  private var value = 0
	  def increment() { value += 1 }
	  def current = value //声明中没有()
	}
```

注意，在getter方法的定义中并没有()。因此，你必须以不带圆括号的方式来调用：

` val n = myCounter.current  //myCounter.current()这样的调用方式是语法错误的 `

总结一下，在实现属性时你有如下四个选择：
1. var foo: Scala自动合成一个getter和一个setter
2. val foo：Scala自动合成一个getter
3. 由你来定义foo和foo_=方法。
4. 由你来定义foo方法

**说明；**在Scala中，你不能实现只写属性（即带有setter但不带getter的属性）。

**提示：**当你在Scala类看到字段的时候，记住它和Java或C++中的字段不同。它是一个私有字段，加上getter方法（对val字段而言）或者getter和setter方法（对var字段而言）。

### 5.4 对象私有字段

在Scala中（Java和C++也一样），方法可以访问该类的所有对象的私有字段。eg：
``` Scala
	class Counter {
	  private var value = 0
	  def increment() { value += 1 }
	  def isLess(other: Counter) = value < other.value //可以访问另一个对象的私有字段
	}
```

之所以访问other.value是合法的，是因为other也同样是Counter对象。

Scala允许我们定义更加严格的访问限制，通过**private[this]**这个修饰符来实现。

` private[this] var value = 0  //类似某个对象.value这样的访问将不被允许 `

这样一来，Counter类的方法只能访问到当前对象的value字段，而不能访问同样是Counter类型的其他对象的该字段。这样的访问有时被称为*对象私有的*，这在某些OO语言，比如SmallTalk中十分常见。

对于类私有的字段，Scala生成私有的getter和setter方法。但对于对象私有的字段，Scala根本不会生成getter或setter方法。

**说明：**Scala允许你将访问权赋予指定的类。private[类名]修饰符可以定义仅有指定类的方法可以访问给定的字段。这里的*类名*必须是当前定义的类，或者是包含该类的外部类。

在这种情况下，编译器会生成辅助的getter和setter方法，允许外部类访问该字段。这些类将会是公有的，因为JVM并没有更细粒度的访问控制系统，并且它们的名称也会随着JVM实现不同而不同。

### 5.5 Bean属性

当你将Scala字段标注为@BeanProperty时，这样的方法会自动生成。eg：

``` Scala
	import scala.reflect.BeanProperty
	class Person {
	  @BeanProperty var name: String = )
	}
```

将会生成四个方法：
1. name: String
2. name_=(newValue: String):Unit
3. getName():String
4. setName(newValue: String):Unit

| **Scala字段** | **生成的方法** | **何时使用** |
| ------- | ------- | ------- |
| val/var name | 公有的name、name_=(仅限于var) | 实现一个可以被公开访问并且背后是以字段形式保存的属性 | 
| @BeanProperty val/var name | 公有的name、getName()、name_=(仅限于var)、setName(...)(仅限于var) | 与JavaBeans互操作 |
| private val/var name | 私有的name、name_=(仅限于var) | 用于将字段访问限制在本类的方法，就和java一样。尽量使用private——除非你真的需要一个公有的属性 |
| private[this] val/var name | 无 | 用于将字段访问限制在同一个对象上调用的方法。并不经常用到 |
| private[类名] val/var name | 依赖于具体实现 | 将访问权赋予外部类。并不经常用到 |

### 5.6 辅助构造器

Scala类有一个构造器比所有构造器都更为重要，它就是主构造器（primary constructor）。除了主构造器之外，类还可以有任意多的**辅助构造器**（auxiliary constructor）。

1. 辅助构造器的名称为this。（在Java或C++，构造器和类名相同——当你修改类名时就不那么方便了。）
2. 每一个辅助构造器都必须以一个对先前已定义的其他辅助构造器或主构造器的调用开始。

这里有一个带有两个辅助构造器的类。

``` Scala
	class Person {
	  private var name = ""
	  private var age = 0
	  
	  def this(name: String) { //一个辅助构造器
	    this() //调用主构造器
		this.name = name
	  }
	  
	  def this(name: String, age: Int) { //另一个辅助构造器
	    this(name) //调用前一个辅助构造器
		this.age = age 
	  }
	}
```

你可以以三种方式构建对象：

``` Scala
	val p1 = new Person //主构造器
	val p2 = new Person("Fred") // 第一个辅助构造器
	val p3 = new Person("Fred", 42) //第二个辅助构造器
```


### 5.7 主构造器

主构造器并不以this方法定义，而是与类定义交织在一起。

1. 主构造器的参数直接放置在类名之后。

``` Scala
	class Person(val name: String, val age: Int) {
	  // (...)中的内容就是主构造器的参数
	  ...
	}
```

主构造器的参数被编译成字段，其值被初始化成构造时传入的参数。在本例中，name和age称为Person类的字段。如new Person("Fred", 42)这样的构造器调用将设置name和age字段。

我们只用半行Scala完成了七行Java代码的工作：

``` Scala
	public class Person { //这是Java
	  private String name;
	  private int age;
	  public Person(String name, int age) {
	    this.name = name;
		this.age = age;
	  }
	  public String name() { return this.name; }
	  public int age() { return this.age; }
	  ...
	}
```

2. 主构造器会执行类定义中的所有语句。

### 5.8 嵌套类
	
	
****未完待续。。。。。。。。。。****