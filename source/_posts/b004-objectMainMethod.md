---
title: object中的main方法
toc: true
date: 2018-05-01 07:20:48
tags:
categories: scala技巧
---

scala对象的main方法可以作为应用或脚本的入口。  

来看一个简单的实例：
```scala
object MyApp {

  def main(args: Array[String]): Unit = {
    println("Hello World!")
  }
}
```

我们也可以通过混入trait App来避免使用main方法：
```scala
object MyApp extends App {

  println("Hello World!")

}
```

这是因为在trait App中实现了main方法。类定义中的初始化代码块内容在编译时都被写入了一个函数对象缓存，直到在调用App的main方法时才会从缓存中取出初始化函数来执行。