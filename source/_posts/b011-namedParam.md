---
title: 带名称的参数值
toc: false
date: 2018-05-06 19:00:40
tags:
categories: scala技巧
---

先来看一段代码，看看执行后会输出什么：
```scala
object MyApp extends App {
  
  def showValue(x: Int, y: Int): Unit = {
    println(s"x is $x")
    println(s"y is $y")
  }

  showValue(y = 3, x = 6)
}
```

这段代码看了以后第一反应是会报错。我将最后一行“showValue(y = 3, x = 6)”中的“y=3”和“x=3”看做简单赋值语句了。scala中赋值语句返回值为Unit，所以执行代码会报类型不匹配的错误。

然而我错了。  

看下执行结果：
```text
x is 6
y is 3
```

最后一行代码中的“y=3”和“x=3”的确是赋值语句，更准确的说法是根据参数名称显式赋值。传递给方法的值就是赋给参数的值，而非Unit。  

一般情况下调用方法或函数，就按照函数定义时的参数顺序一个个传递。但是也可以通过指定参数名赋值，并且不需要按照定义时的顺序向函数传递参数。