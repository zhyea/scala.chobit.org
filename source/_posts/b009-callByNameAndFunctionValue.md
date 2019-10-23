---
title: 参数中的 =>U 和 ()=>U
toc: false
date: 2018-05-06 18:06:30
tags:
categories: scala技巧
---

在scala参数中会遇到=>U 或者 ()=>U。U表示返回值类型。简单说下二者的区别。  

=>U是一个传名参数，表示一个无参方法。  

()=>U则是一个函数。

举个例子，看一下二者的区别：
```scala
object MyApp extends App {

  def mTime: Long = System.currentTimeMillis()

  val fTime = ()=>System.currentTimeMillis()

  def job(t1: => Long, t2: ()=>Long) = {
    println(s"start time: $t1")
    for (i <- 0 to 6) Thread.sleep(i * 100)
    println(s"end time: ${t2()}")
  }

  job(mTime, fTime)
}
```


关于方法和函数的关系可以参考[之前的一篇文](/wiki/20180501/a15-methodVsFunction/)。  

还有篇关于传名参数的文：[传名参数](/wiki/20180506/a18-callByName/)

