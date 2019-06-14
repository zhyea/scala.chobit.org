---
title: trait和抽象类
toc: true
date: 2018-05-30 21:59:06
tags: [trait,抽象类]
categories: scala学习
---

发现前面多次提到了抽象类，可是却从没有正式的介绍过抽象类这个概念。在这里一起说下。

## 抽象类

scala的抽象类的定义和Java的差不多。使用了关键词abstract声明的类叫作抽象类。如果一个类里包含了一个或多个抽象方法，该类就必须被定义为抽象类。抽象方法属于一种不完整的方法，抽象方法只有方法声明，没有方法体。

下面的代码演示了抽象类的定义和实现：

```scala
abstract class Teacher(val name: String) {

  val age: Int

  def teach(): Unit
}

class MathTeacher(override val name: String) extends Teacher(name) {

  override val age = 29

  override def teach(): Unit = println("teaching Math");
}

object MyApp extends App {

  val teacher = new MathTeacher("tom")

  teacher.teach
}
```

在上面的代码中定义了抽象类Teacher，抽象类中有抽象字段（name和age）和抽象方法（teach()方法）。

抽象类有如下几个特征：

1. 在普通类中 字段必须赋值(包括赋具体的值和使用下划线为var变量赋默认值) 
1. 抽象类不需要初始化
1. 抽象类中可以有抽象方法 
1. 只定义不赋值的字段为抽象字段 
1. 只声明而不实现的方法为抽象方法；和java不同，scala的抽象方法不强制使用abstract关键字声明，只需不写方法体即可

## trait和抽象类

介绍下trait和抽象方法的异同。

二者的相似之处：

1. 都可以有普通方法和抽象方法；
2. 都可以有普通成员变量和抽象变量；
3. 抽象类能做的事情trait都能做。

二者的不同之处：

1. trait可以多重混入，抽象类只能单继承；
1. 抽象类可以定义构造函数；
1. trait可以混入object，抽象类不可以。

那什么时候用trait，什么时候用抽象类呢：

1. 优先使用trait。一个类扩展多个trait是很方便的，但却只能扩展一个抽象类。
1. 如果你需要构造函数参数，使用抽象类。因为抽象类可以定义带参数的构造函数，而trait不行。例如，不能定义trait T(i: Int) {}，参数i是非法的。