---
title: apply方法
toc: true
date: 2018-06-07 22:37:03
tags: apply
categories: scala技巧
---

scala的apply方法有一个特性：就是在调用apply方法的时候可以省略方法名，即可以用 “**类名或实例名 + ([参数列表])**”这样的形式来使用。

scala中数组的定义和使用就是apply方法的一个典型实例，来看一下：

```bash
scala> val arr = Array(1,3,5,7,9)
arr: Array[Int] = Array(1, 3, 5, 7, 9)

scala> arr(3)
res0: Int = 7
```
构建数组实例的时候调用了object Array的一个apply方法，通过索引获取数组元素的时候调用的是class Array的一个apply方法。调用这两apply方法的时候，方法名被省略掉了，但是在使用上却更直观了。

通过刚才这个数组的例子，我们了解到在scala中apply方法有两种，一种是object中的apply方法，一种是class中的apply方法。接下来我们会尝试自己定义使用这两种apply方法。

先来看下class中apply方法的使用：

```scala
class Coder(name: String) {

  def apply() = print(s"$name is a coder.")

  def apply(job: String) = println(s"$name's job is $job.")

}


object MyApp extends App {

  val c = new Coder("robin")

  c()
  c("coding")

}
```

可以看到class的apply方法的作用就是可以通过实例并省略方法名来调用。

再来看下object的apply方法，其实也是一样的：

```scala
object Coder {

  def apply(name: String): Coder = new Coder(name)

  def apply() = println("do nothing")
}


object MyApp extends App {

  val c = Coder("robin")
  Coder()
}
```

apply方法的作用就是这样：可以让开发者更简单的调用apply方法。

上面的例子我做了一个不好的示范：因为这几个apply方法大多定义得没有意义——除了伴生对象中的创建实例的那个。apply方法是如此特殊，在使用的时候也应慎重。