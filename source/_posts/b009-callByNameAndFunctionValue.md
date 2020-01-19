---
title: 参数中的 =>U 和 ()=>U
toc: false
date: 2018-05-06 18:06:30
tags:
categories: scala技巧
---

在scala参数中会遇到=>U 或者 ()=>U。U表示返回值类型。简单说下二者的区别。  

* =>U是一个传名参数，表示一个**无参**方法。  
* ()=>U则是一个函数。

举个例子，看一下二者的区别：
```scala
object MyApp extends App with Logging {

	def mTime: Long = System.currentTimeMillis()

	val fTime = () => System.currentTimeMillis()

	def job(t1: => Long, t2: () => Long) = {
		for (i <- 0 to 3) {
			println(s"start time: $t1")
			println(s"  end time: ${t2()}")
			TimeUnit.SECONDS.sleep(1L)
			println("------------------------------------------------------------")
		}
	}

	job(mTime, fTime)

}
```

从代码中可以看出二者的区别：

* 定义上，传名参数用**def**定义，函数使用**val**定义，且传名参数方法要求参数为空
* 使用时，虽然二者都是调用时再进行计算，但是函数需要加上“()”转为方法再调用，传名参数本身即是方法不需要转


关于方法和函数的关系可以参考[之前的一篇文](/wiki/20180501/a15-methodVsFunction/)。  

还有篇关于传名参数的文：[传名参数](/wiki/20180506/a18-callByName/)

