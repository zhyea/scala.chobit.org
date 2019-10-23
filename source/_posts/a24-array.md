---
title: 数组
toc: true
date: 2018-05-27 22:04:05
tags: 数组
categories: scala学习
---

简单说下scala中的数组。

## 声明数组

声明数组的语法：
```scala
val arr = new Array[String]\(3\)
```
声明数组时必须指定数组的长度。数组的类型可以不指定，但是不指定的话，数组就是无用的，因为此时数组元素的类型是Nothing。

这样声明的数组其元素初始为字符串的默认值，即null。可以这样调整数组元素的值：
```scala
  arr(0) = "com"
  arr(1) = "zhyea"
  arr(2) = "robin"
```
可以看到scala中获取数组元素的方法是：数组名 + 小括号 + 索引值。这里究其根本还是调用了Array类的apply(i:Int)方法，因此索引值也可以是一个表达式，比如arr(4/2)。

也可以在创建数组时完成初始化：
```scala
val arr = Array[String]("com", "zhyea", "robin")
```

## 多维数组

调用object Array的ofDim方法可以创建一维到五维的数组。如下是一个创建二维数组的示例：
```scala
val matrix = Array.ofDim[Int](2, 2)
```

值的一提的是，在scala中本质上也是没有多维数组的。同java一样，所谓的多维数组其实质也是一维数组的嵌套（或者说是数组的数组）。这在object Array的二维数组的实现中可以很明显地看出来：

```scala
  def ofDim[T: ClassTag](n1: Int, n2: Int): Array[Array[T]] = {
    val arr: Array[Array[T]] = (new Array[Array[T]](n1): Array[Array[T]])
    for (i <- 0 until n1) arr(i) = new Array[T](n2)
    arr
  }
```

## 变长数组

这里说的变长数组就是ArrayBuffer。ArrayBuffer更应该被归类到集合里面，但是既然看到了就一起写出来好了。在使用方式上ArrayBuffer有些类似于java里的ArrayList，但是要更灵活一些：

```scala
import scala.collection.mutable.ArrayBuffer

object MyApp extends App {

  val arr = ArrayBuffer[Int]()

  arr += 1 //尾端添加一个元素

  arr ++= Array[Int](2, 3, 4) //尾端添加一个数组

  arr += (5, 6, 7) // 尾端添加多个元素

  println(arr.mkString(", "))
}
```

最终执行结果为：
```text
1, 2, 3, 4, 5, 6, 7
```

ArrayBuffer和Array可以互相转换，只需分别调用toArray()和toBuffer()方法即可。

## 数组应用

关于数组觉得比较有趣的一个应用是在和变量一起的使用的时候，比如下面：
```scala
  val Array(a, b, c) = Array[Int](1, 2, 3)

  println(a)
  println(b)
  println(c)
```
代码中通过一个数组完成了a、b、c三个变量的赋值。

执行结果如下：
```text
1
2
3
```

至于数组其他的应用，如循环、拼接、copy等操作，在使用的时候可以翻看一下API。
