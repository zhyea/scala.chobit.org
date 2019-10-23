---
title: 基本类型
toc: true
date: 2018-04-23 06:46:39
tags: 基本类型
categories: scala学习
---


先声明一点，scala中是没有基本数据类型的。在scala中一切都是对象。  

在Java中数据类型分为基本类型和引用类型。虽然在JDK1.5以后引入了自动装箱和自动拆箱机制，简化了一些基本类型和引用类型的转换问题。但是我们仍然会遇到一些问题：
* 默认值的问题，“Intege i”和“int i”声明的整型数值的默认值是不同的；
* 泛型的问题，List&lt;int&gt;这样的写法在Java中是不允许的；
* 直接量是不可以调用对象方法的，如1.toString()在Java中就会报错。 

在scala中不会有这样的问题。一开始就提过了：在scala中一切都是对象。比如1.toString()这样的写法在scala中是完全可以的。对应着Java的每个基本类型，scala都有一个封装类，包括Byte、Char、Short、Int、Long、Float、Double和Boolean。scala的封装类和Java的基本类型是可以直接转换的，比如下面这段代码：
```scala
class ScalaInt {
	def playWithInt() {
		val capacity: Int = 10
		val list = new java.util.ArrayList[String]  
		list.ensureCapacity(capacity)
	}
}
```
在这段代码中首先声明了一个scala.Int类型的值capacity，而后调用java API创建了一个ArrayList实例。ArrayList实例的ensureCapacity方法参数类型是一个Java的整型值。但是，我们直接将scala.Int类型的值capacity传递过去也并没有报错。在这里scala.Int变量capacity被隐式转换为java直接类型的变量。  

值得一提的是，在封装类之外，scala还有一种富封装类。比如，稍后我们用到循环的时候，通常会有这样的写法：
```scala
for(i <- 1 to 3>){
    print(i)
}
```
代码里有“1 to 3”或者说“1.to(3)”，其中的to方法不是scala.Int类型的方法，而是其对应的富封装类scala.runtime.RichInt中的方法。当scala推断出Int型的值1无法满足要求时就会调用它的intWrapper方法将其转为scala.runtime.RichInt类型，而后再调用它的to方法。  

在scala中直接量的封装类提供了算术运算方法，富封装类则封装了一些更常用的方法以方便开发使用。

