---
title: 模式匹配
toc: true
date: 2018-06-20 21:45:14
tags: [match,case,模式匹配]
categories: scala学习
---

## 概述

在java中有switch/case这样的模式匹配语句，可以匹配的类型包括int,byte,char,short, enum，在java8又支持了字符串。

在scala中也有类似的模式匹配语句，即match-case。scala中的match-case匹配的类型更为广泛：它是对Any类型起作用的。

来看个例子：

```scala
  def activity(day: String) {
    day match {
      case "Sunday" => println("Eat, sleep, repeat... ")
      case "Saturday" => println("Hang out with friends... ")
      case "Monday" => println("...code for fun...")
      case "Friday" => println("...read a good book...")
    }
  }

  List("Monday", "Sunday", "Saturday").foreach {
    activity
  }
```

这段代码的执行结果如下：

```text
...code for fun...
Eat, sleep, repeat... 
Hang out with friends... 

Exception in thread "main" scala.MatchError: Tuesday (of class java.lang.String)
	at com.zhyea.MyApp$.activity(MyApp.scala:7)
	at com.zhyea.MyApp$$anonfun$1.apply(MyApp.scala:16)
	at com.zhyea.MyApp$$anonfun$1.apply(MyApp.scala:16)
```

有没有注意到，这里和java是有些不一样的，最大的区别是没有用到break关键字。但是在scala中默认是匹配上了后就不会继续匹配了。窃以为这是比java做得好的地方。

通过执行结果可以看到scala的模式匹配有一个需要注意的地方：没有匹配到会抛出异常。这一点在使用的时候要小心。

在Java的switch/case中，可以用**default**关键字来执行默认通用匹配处理。在scala中也有符号起到了类似的作用，就是“_”。继续修改下上面的代码：

```
def activity(day: String) {
  day match {
    case "Sunday" => println("Eat, sleep, repeat... ")
    case "Saturday" => println("Hangout with friends... ")
    case "Monday" => println("...code for fun...")
    case "Friday" => println("...read a good book...")
    case _ => println("...nothing...")
  }
}

List("Monday", "Sunday", "Saturday", "Tuesday").foreach {
  activity
}
```
就不贴执行结果了。


## 多类型匹配

case表达式并不限于在match语句里使用。这里，包含case表达式的代码块就是一个简单函数值。这一点在前面学习[偏函数](wiki/20180508/a20-partialFunction/)的时候也有提到过。

刚才说过match-case支持的类型是Any。在java支持的基本类型、枚举和字符串之外，scala还支持支持其他任意类型的模式匹配，比如列表和元组：

```scala
  def processCoordinates(input: Any) {
    input match {
      case (a, b) => printf("Processing (%d, %d)... ", a, b)
      case List("red", "blue", "white") => println("Stars and Stripes...")
      case List("red", "blue", _*) => println("colors red, blue, ... ")
      case "done" => println("done")
      case _ => null
    }
  }

  processCoordinates((39, -104))
  processCoordinates("done")
  processCoordinates(List("red", "blue", "green"))
  processCoordinates(List("red", "blue", "white"))
```

看下执行结果：

```text
Processing (39, -104)... done
colors red, blue, ... 
Stars and Stripes...

Process finished with exit code 0
```

## 匹配时类型声明和卫述语句

上面的代码还是有些不足之处，对元组的匹配还可以更细致些，比如针对不同类型的值可以做不同的处理：

```scala
  def process(input: Any) {
    input match {
      case (a: Int, b: Int) => println("Processing (int, int)... ")
      case (a: Double, b: Double) => println("Processing (double, double)... ")
      case msg: Int if (msg > 1000000) => println("Processing int > 1000000")
      case msg: Int => println("Processing int... ")
      case msg: String => println("Processing string... ")
      case _ => printf("Can't handle %s... ", input)
    }
  }

  process((34.2, -159.3))
  process(0)
  process(1000001)
  process(2.2)
```

代码中演示了在case语句里如何为单一的值和元组元素指定类型。除了类型之外，还可以使用卫述句（guard）。卫述句用if从句表示，在模式匹配里，对表达式求值前必须满足卫述句。

代码中用到了占位符，即：a, b, msg。通过占位符可以比较方便地调用进行匹配的值。

看下结果：

```text
Processing (double, double)... 
Processing int... 
Processing int > 1000000
Can't handle 2.2... 
Process finished with exit code 0
```

case的顺序很重要。Scala会自上而下地求值。所以，上面代码中的5和6两行是不能交换的。

