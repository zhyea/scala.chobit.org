---
title: implicit-scala隐式处理
toc: true
date: 2018-05-31 06:07:47
tags: implicit
categories: scala学习
---
## 概述

先来看看下面的内容：
```scala
2 days “ago”
5 days “from_now”
```

这段内容大家怎么看。首先这确定是一段代码（不是代码也没有必要提了），但是也可以确定的是scala的整型是没有days这样的方法的——在Int、RichInt还有Java的Integer中都没有days方法。  

那么是怎样为整型值添加days方法的，或者说是如何扩展整型的方法的。  

在scala中我们可以使用隐式类型转换来扩展类的能力。关于隐式类型转换，我手边的教材是这样说的：

> 隐式类型转换可以帮助我们扩展语言，创建“专用于特定应用和领域”的词汇或语法，也可以帮助我们创建属于自己的领域专用语言。

这句话说明了隐式类型转换的主要作用。

再来看看隐式类型转换具体是怎样发挥作用的。还以最初的那段代码为例，看一下是怎样为整型添加days方法的。

首先，我们需要创建一个DateHelper类：

```scala
class DateHelper(number: Int) {

  def days(when: String): Date = {
    val date = Calendar.getInstance()
    when match {
      case "ago" => date.add(Calendar.DAY_OF_MONTH, -number)
      case "from_now" => date.add(Calendar.DAY_OF_MONTH, number)
      case _ => date
    }
    date.getTime()
  }
}
```

可以看到在DateHelper方法中定义了days方法。可以明确地说这个days方法就是一开始的示例中的days方法。DateHelper类还有一个整型值的构造器。那么怎样使用隐式类型转换让整型值可以调用DateHelper的days方法？我们需要使用**implicit**关键字，如下：
```scala
implicit def convertInt2DateHelper(number: Int): DateHelper = new DateHelper(number)
```
这段代码将一个整型值转为了DateHelper实例。此时再让整型值调用days方法就可以了：
```scala
  implicit def convertInt2DateHelper(number: Int): DateHelper = new DateHelper(number)

  println(1 days "ago")
```

输出结果如下：
```text
Tue Jun 05 07:09:29 CST 2018
```

## 隐式转换函数

刚才的例子就是一个隐式转换函数的实例。在这个例子中定义的隐式转换函数为convertInt2DateHelper。通过这个函数我们可以将DateHelper类的能力提供给一个整型值。另外，需要啰嗦一下：隐式转换函数的名称对其功能没有任何影响，只要能表达其含义，具备可读性即可。

接下来我们尝试优化一下前面的实例中的隐式转换。每次需要进行隐式转换时就定义一个隐式转换函数并不是一个很好的做法。我们可以把这个隐式转换函数定义在某个位置（比如某个包或某个类），在需要的时候import过来即可。对于我们这个实例来说，一个比较理想的位置就是DateHelper类的伴生对象了。来看下调整后的代码：

```scala
import java.util.{Calendar, Date}

class DateHelper(number: Int) {

  def days(when: String): Date = {
    val date = Calendar.getInstance()
    when match {
      case "ago" => date.add(Calendar.DAY_OF_MONTH, -number)
      case "from_now" => date.add(Calendar.DAY_OF_MONTH, number)
      case _ => date
    }
    date.getTime()
  }
}

object DateHelper {
  implicit def convertInt2DateHelper(number: Int): DateHelper = new DateHelper(number)
}

object MyApp extends App {

  import DateHelper._

  println(1 days "ago")

}
```
在MyApp类中我们通过“ import DateHelper._”调用了DateHelper object中的convertInt2DateHelper函数完成了隐式转换。如果应用场景类没有和DateHelper类在同一个包下，那么就可以在顶部引用中引入convertInt2DateHelper函数并进行使用：
```scala
import com.zhyea.DateHelper._

object MyApp extends App {
  
  1 days "ago"
}
```

## 隐式参数

一般定义的函数中，通常需要明确传入参数，如下例：

```
  def hello(name: String): Unit = {
    println(s"Hello $name!")
  }

  hello("robin")
```

但是如果函数的参数是隐式参数的话，则可以不必传入：
```scala
  def hello(name: String)(implicit gender: String): Unit = {
    gender match {
      case "male" => println(s"Hello Mr $name!")
      case "female" => println(s"Hello Ms $name!")
      case _ => println(s"Hello $name!")
    }

  }

  implicit val gender: String = "male"

  hello("robin")
```

声明函数时，隐式参数用implicit关键字声明，并以curry化形式使用。

调用含有隐式参数的函数时，可以不传入隐式参数，但是在调用隐式参数的上下文中，必须能找到可用的隐式参数。也就是说，在如下的情况下，调用hello函数也是OK的（因为隐式变量是在调用函数后提供的，执行结果是不一样的）：
```scala
  hello("robin")

  implicit val g: String = "male"
```

但是如果不提供任何可用的隐式变量，就不能通过编译了。


## 隐式处理规则

在scala中，隐式处理是普遍存在的，比如我们可以执行“1 to 7”这样的语句，就是因为Scala隐式地将1从Int类型转换为其富封装器RichInt类型。  

Int到RichInt的转换是定义在Predef对象中的（确切说是在Predef的超类LowPriorityImplicits中）。Predef对象中封装了一系列隐式类型转换函数定义。在我们每次执行scala程序时都会默认引用Predef对象，因此也默认引用了这些隐式转换函数。可以看几个Predef中定义的隐式转换函数：
```scala
  implicit def byte2Byte(x: Byte)           = java.lang.Byte.valueOf(x)
  implicit def short2Short(x: Short)        = java.lang.Short.valueOf(x)
  implicit def char2Character(x: Char)      = java.lang.Character.valueOf(x)
  implicit def int2Integer(x: Int)          = java.lang.Integer.valueOf(x)
  implicit def long2Long(x: Long)           = java.lang.Long.valueOf(x)
  implicit def float2Float(x: Float)        = java.lang.Float.valueOf(x)
  implicit def double2Double(x: Double)     = java.lang.Double.valueOf(x)
  implicit def boolean2Boolean(x: Boolean)  = java.lang.Boolean.valueOf(x)
```
上面是scala使用隐式转换处理Java基本类型自动装箱和自动拆箱的代码。

那么什么时候会进行隐式处理呢？根据我们前面的实例可以推测出大致有这几种情况：

1. 调用方法时使用的参数列表和定义的参数列表不一致时；
1. 值的类型与预期类型不一致时；
1. 调用类中不存在的方法或成员时。

第二种情况前面没有提到过，用scala解释器演示下：
```bash
scala> implicit def double2Int(i:Double)=i.toInt
warning: there was one feature warning; re-run with -feature for details
double2Int: (i: Double)Int

scala> def foo(x:Int)=print(x)
foo: (x: Int)Unit

scala> foo(3.14)
3
```

这里演示的是调用方法时产生了隐式转换。也许像下面这样更直观一些：
```bash
scala> val i:Int = 3.14
i: Int = 3
```
需要Int值，却提供了一个浮点值。scala检查到当前上下文中存在整型值到浮点值的隐式转换函数，就调用隐式转换函数将浮点值转为了整型值。

简而言之：在当前范围内，如果发现通过类型转换有助于操作、方法调用或类型转换的成功完成，就会进行转换。

再看下不进行隐式处理的情况，大概有这么几种：

1. 如果没有隐式转换函数或隐式变量，编译也可以通过，说明不需要进行隐式处理；
1. 如果存在二义性，则无法进行隐式处理；
1. 隐式处理无法嵌套执行，一次至多只能应用一次隐式转换。

解释下后两种情况。关于二义性，看下这个例子：
```bash
scala> implicit def double2Int(i:Double)=i.toInt
double2Int: (i: Double)Int

scala> implicit def doubleToInt(i:Double)=i.toInt
doubleToInt: (i: Double)Int

scala> val i:Int = 3.14
<console>:16: error: type mismatch;
 found   : Double(3.14)
 required: Int
Note that implicit conversions are not applicable because they are ambiguous:
 both method double2Int of type (i: Double)Int
 and method doubleToInt of type (i: Double)Int
 are possible conversion functions from Double(3.14) to Int
       val i:Int = 3.14
                   ^
```
在解释器中定义了两个将整型转为浮点型的函数，两个函数的处理方式是一样的，只是名称略有不同，然而在编译时报错了，提示ambiguous，即存在二义性。

为了避免二义性，在同一个作用范围内，尽量不要出现相同类型的隐式变量和隐式函数。

至于第三点，可以看一下下面的例子：
```bash
scala> implicit def double2Int(i:Double)=i.toInt
double2Int: (i: Double)Int

scala> implicit def int2Boolean(i:Int)=i > 2
int2Boolean: (i: Int)Boolean

scala> val b:Boolean = 3.14
<console>:13: error: type mismatch;
 found   : Double(3.14)
 required: Boolean
       val b:Boolean = 3.14
                       ^
```
在解释器中尝试将一个浮点值赋值给一个boolean型的变量。这里期望先为这个浮点值调用double2Int函数隐式转换为整型值，然后在调用int2Boolean函数隐式转换为boolean类型。不过程序并没有按照预期的路线一个接一个的嵌套执行隐式转换，而是直接报错了。
