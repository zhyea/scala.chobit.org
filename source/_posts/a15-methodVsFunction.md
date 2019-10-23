---
title: 方法VS函数
toc: true
date: 2018-05-01 10:23:25
tags: 
categories: scala学习
---




在scala中，我们可以认为函数和方法是两个不同的概念——尽管很多时候它们是通用的。这一节简单介绍下二者之间的关系。

## 方法和函数的不同

函数在定义和调用等方面有着不同。先来看一下。

### 定义

scala中的方法是类的一部分，方法不能脱离类独立存在。

scala中的函数是一个对象。scala用22个trait（包括Function1~Function22，以下简称FunctionN）抽象出了函数的概念。scala中的函数就是实现了这些trait的对象。它的表现形式通常为“(T1, ..., Tn) => U”。

### 声明

下面是一个方法的声明：
```scala
  def add(x: Int, y: Int): Int = {
    x + y
  }
```

下面是一个相同功能的函数的声明：
```scala
  val multi = (x: Int, y: Int) => {
    x * y
  }
```

可以看到声明函数声明方法存在着明显的不同：
* 声明方法使用的是def关键字，声明函数使用的是val关键字
* 函数不需要(不可以)显式注明返回值的类型，方法的返回值类型是可选的
* 函数的表现形式为：(T1, ..., Tn) => U

函数本质上是一个值（也称函数值），我们定义的multi函数其意义等同于下面的代码：
```scala
  val multi = new Function2[Int, Int, Int](){
    override def apply(x: Int, y: Int): Int = x * y
  }
```
这里代码中的multi实际上就是依赖trait Function2实现的一个匿名类实例。Function2有三个类型参数[Int, Int, Int]，前两个是apply方法的参数类型，最后一个是apply返回值类型。

apply是scala提供的一个语法糖，比如对object A调用A()，scala编译器会自动将之转换为A.apply()。对一个class B调用B()，就是在调用B的伴生对象的apply方法。

应该能注意到Function2对应的函数有两个参数。scala就是这样设计的：FunctionN对应的函数有N个参数。所以scala的函数的参数长度是有限的。

既然函数本质上是一个对象，那么声明函数也可以使用关键字var：
```scala
var multi = (x:Int, y:Int) => x * y
```
在为var函数值变量重新赋值时遇到了一个问题：
```bash
scala> var multi = (x: Int, y: Int) => x * y
multi: (Int, Int) => Int = <function2>

scala> multi = (x:Int) => x * 1
<console>:12: error: type mismatch;
 found   : Int => Int
 required: (Int, Int) => Int
       multi = (x:Int) => x * 1
                       ^
```
重新赋值时要求新的函数值的参数列表与原函数值一致。这是因为scala的类型推断能力，在初始化multi函数值时就将multi的类型推断为Function2[Int, Int, Int]。当为multi重新赋值时，就只能赋值为Function2[Int, Int, Int]的实例，也就是只能赋值为相同参数类型且相同返回值类型的函数。

### 方法和函数的调用

方法名就意味着方法的调用。而函数因为本身是一个值，函数名只能代表函数自身。

我们定义一个无参的方法和一个无参的函数来做一下演示：
```bash
scala> def m() = 100
m: ()Int

scala> val f = ()=>100
f: () => Int = <function0>

scala> m
res1: Int = 100

scala> f
res2: () => Int = <function0>
```
使用方法名时可以看到是调用了方法，使用函数名时则与声明函数时返回的信息相同。

如果要调用一个函数，可以在函数名后面加上()：
```bash
scala> f()
res3: Int = 100
```

### 参数列表

方法和函数都有参数列表，它们的参数列表的长度都可以为0。  

方法可以没有参数列表，比如“def m=100”就是正确的。  

函数则不能没有参数列表，如“val f = =>100”就一定会报错。函数的参数列表长度可以为0，比如“()=>100”

### 最终表达式

因为函数本质上是一个值，所以函数可以作为最终表达式使用。
```bash
scala> val multi = (x:Int, y:Int) => x*y
multi: (Int, Int) => Int = <function2>

scala> multi
res5: (Int, Int) => Int = <function2>
```

方法不能作为最终表达式使用。当方法有参数时或者说参数长度大于0时，直接使用方法名就会报错。当方法参数长度为0时，直接使用方法名等同于调用方法。
```bash
scala> def add(x:Int, y:Int) = x+y
add: (x: Int, y: Int)Int

scala> add
<console>:13: error: missing argument list for method add
Unapplied methods are only converted to functions when a function type is expected.
You can make this conversion explicit by writing `add _` or `add(_,_)` instead of `add`.
       add
       ^
```

## 方法和函数的转换

在本文一开始我们提到过scala的函数和方法很多时候是通用的，这里是指scala中的函数和方法是可以互相转换的。这种转换很多时候还是隐式的。

### 将方法转换为函数

scala的一些高级函数比如map()、filter()、foreach()等，需要传递一个函数值作为参数。我们可以直接传递一个方法给这些高级函数：
```bash
scala> val list = List(1,2,3,4)
list: List[Int] = List(1, 2, 3, 4)

scala> val f = (n:Int)=>print(n)
f: Int => Unit = <function1>

scala> list.foreach(f)
1234

scala> def m(n:Int) = print(n)
m: (n: Int)Unit

scala> list.foreach(m)
1234
```
在代码中我们定义了一个List实例，我们主要调用了list的foreach方法。foreach方法的参数是一个函数值。通过foreach方法我们可以遍历List实例，并在函数值中调用List实例的每个值。  

我们先为foreach方法提供了一个函数值f。函数值的功能很简单，就是打印出当前遍历的值。然后又为foreach方法提供了一个方法m作为参数，方法m和函数f提供的能力是相同的，我们可以看到执行结果也是相同的。

这种在期望出现函数的地方提供了一个方法，方法被自动转换成函数的行为被称为ETA展开。

注意，虽然scala提供了ETA展开的能力，但是不能直接将方法赋值给变量作为函数值。

刚才我们说的是一种隐式转换的例子。我们还可以显式地将方法转为函数，做法很简单，就是在方法名后面加一个下划线：
```bash
scala> def add(x:Int, y:Int) = x +y
add: (x: Int, y: Int)Int

scala> add _
res12: (Int, Int) => Int = <function2>
```
注意方法名后的下划线和方法名中间需要存在一个空格。

scala还有一种特殊的参数叫做传名参数，如下：
```scala
def delayed( t: => Long ) = {
      println("参数： " + t)
      t
  }

def time() = System.currentTimeMillis

delayed(time())
```
看起来有点儿像是函数值的使用，但是传名参数实质是一个没有参数列表的方法，因此使用参数名字的时候是调用了对应的方法。

### 将函数转换为方法

将函数转换为方法的做法前面提到过：在函数名后面添加()和参数即可。
```bash
scala> val multi = (x:Int, y:Int) => x*y
multi: (Int, Int) => Int = <function2>

scala> multi(1,2)
res13: Int = 2
```
这一节就先介绍这些内容。如果想深入了解可以参考下[Scala 语言规范](/download/ScalaSpecification.pdf)。