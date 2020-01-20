---
title: 集合操作partition
toc: true
date: 2018-06-20 21:45:14
tags: [partition]
categories: scala学习
---

简单介绍下scala集合中的partition操作。确切地说，该方法是属于`scala.Iterable`的。

根据文档来看，该方法的作用是通过一个predicate函数参数将当前集合拆分成两部分。predicatte函数检验返回值为true的元素放在第一个子集合里面，返回值为false的放第二个子集合里面。

该方法的定义如下：

```scala
  def partition(p: A => Boolean): (C, C) = {
    val first = new View.Filter(this, p, false)
    val second = new View.Filter(this, p, true)
    (fromSpecific(first), fromSpecific(second))
  }
```

写了一个示例代码来做验证：

```scala
object MyApp extends App {

	val list = List(1, 2, 3, 4, 5, 6, 7, 8, 9)

	val pair = list.partition(_ > 3)

	println(pair._1)
	println(pair._2)
}
```

predicate函数是`_ > 3`，预期以3作为分界点，将原集合list将分成两部分。看下执行结果：

```text
List(4, 5, 6, 7, 8, 9)
List(1, 2, 3)
```

结果与预期是一致的。