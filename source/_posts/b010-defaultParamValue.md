---
title: 参数默认值
toc: false
date: 2018-05-06 18:45:01
tags:
categories: scala技巧
---

Scala可以为方法参数指定默认值。  
使用了默认参数，在调用方法时可以不需要再传递参数，此时函数就会调用默认参数值，如果传递了参数值，则传递值会取代默认值。

举个例子：
```scala
  def no(no: Int = 1) = no

  println("Default No. is " + no())

  println("This No. is " + no(9))
```

执行结果为：
```text
Default No. is 1
This No. is 9
```