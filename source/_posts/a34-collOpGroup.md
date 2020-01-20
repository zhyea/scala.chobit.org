---
title: 集合操作group
toc: true
date: 2020-01-20 22:45:14
tags: [group]
categories: scala学习
---

在scala的集合中有如下几种group操作：

* groupBy：按集合元素的特征值将集合拆分成Map结构
* grouped：将集合拆分成指定长度的子集合
* groupMap：在执行group操作的同时也执行了map操作
* groupMapReduce：在执行group操作的同时执行了MapReduce操作

## groupBy操作

执行groupBy操作的代码如下：

```scala
	val list = List((1, 'a'), (2, 'b'), (3, 'c'), (1, 'e'), (1, 'f'))

	val map = list.groupBy(_._1)

	println(map)
```

代码中将一个二维元组集合用元组的第一个值进行拆分，拆分结果是一个Map结构。

执行结果如下：

```text
HashMap(1 -> List((1,a), (1,e), (1,f)), 2 -> List((2,b)), 3 -> List((3,c)))
```

## grouped操作

执行grouped操作的代码如下：

```scala
	val list = List(1, 2, 3, 4, 5, 6, 7, 8, 9)

	val itr = list.grouped(4)

	itr.foreach(println)
```

代码中尝试将一个长度为9的集合进行切分，预期切分后的子集合为4。

执行结果如下：

```text
List(1, 2, 3, 4)
List(5, 6, 7, 8)
List(9)
```

## groupMap操作

示例代码如下：

```scala
	val list = List((1, 'a'), (2, 'b'), (3, 'c'), (1, 'e'), (1, 'f'))

	val map = list.groupMap(_._1)(_._2)

	println(map)
```

groupMap操作需要使用两个函数参数：第一个函数参数执行group操作，第二个参数执行map操作。

示例代码在按元组第一个值进行group的同时也对元组执行了map操作：用元组的第二个值替换元组。

执行结果如下：

```text
Map(1 -> List(a, e, f), 2 -> List(b), 3 -> List(c))
```

## groupMapReduce操作

这个方法无非是在groupMap操作上又添加了一层reduce操作。看下示例代码：

```scala
	val list = List((1, 'a'), (2, 'b'), (3, 'c'), (1, 'e'), (1, 'f'))

	val map = list.groupMapReduce(_._1)(_._2.toString)((e1, e2) => e1 + "-" + e2)

	println(map)
```

该操作有三个函数参数，第三个函数参数就是用来执行reduce操作的。

在示例代码中，map操作将元组转为了元组第二个元素对应的字符串，reduce操作则使用横杠(“-”)将字符串进行了拼接。

执行结果如下：

```text
Map(1 -> a-e-f, 2 -> b, 3 -> c)
```

