---
title: 传名参数 =>Unit
toc: true
date: 2018-05-06 07:09:35
tags:
categories: scala学习
---

Scala的解释器在解析函数参数(function arguments)时有两种方式：
* 传值调用（call-by-value）：先计算参数表达式的值，再应用到函数内部；
* 传名调用（call-by-name）：将未计算的参数表达式直接应用到函数内部。

以上是比较严谨的说法。简单解释下，传值调用就是我们常见的方法调用方式，将一个值作为参数传递给方法或函数。java中的方法都是传值调用。传名调用，则是将一个方法作为参数传递给其他方法或函数。

传名调用的参数是方法而非函数，且要求作为参数的方法的参数为空。

来看一个传名参数的例子：
```scala
object MyApp extends App {

  def time: Long = System.currentTimeMillis()

  def job(t: => Long) = {
    println(s"start time: $t")
    for (i <- 0 to 6) Thread.sleep(i * 100)
    println(s"end time: $t")
  }

  job(time)
}
```

总结下传名参数的优势：
* 进行实时运算；
* 如果没有被用到，就可以不必进行运算。