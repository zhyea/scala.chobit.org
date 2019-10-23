---
title: 类继承
toc: true
date: 2018-04-27 05:57:16
tags: 类继承
categories: scala学习
---

在scala里，类继承有两点限制：  
* 重写方法需要使用override关键字；
* 只有主构造函数才能往父类构造函数中传参数。

在java1.5中引入了override注解，但不强制使用。不过在scala中要想重写方法必须使用override关键字。如果确实重写了父类的方法又不使用override关键字的话，则会在编译时报错，提示没有使用override修饰符。

scala的副构造函数必须调用主构造函数或是另一个副构造函数。只有在主构造函数中才能向父类的构造函数中传递数据。可以看出来主构造函数如同父类和子类中间的一道关卡：它既负责完成子类的初始化，也负责与父类的沟通。  

下面的这段代码演示了下上述内容：

```scala
class Vehicle(val id: Int, val year: Int) {
  override def toString(): String = "ID: " + id + " Year: " + year
}

class Car(override val id: Int, override val year: Int, var fuelLevel: Int) extends Vehicle(id, year) {
  override def toString(): String = super.toString() + " Fuel Level: " + fuelLevel
}
```

在代码中定义了两个类：Vehicle和Car。Car继承了Vehicle。  
在继承的时候要向父类传递参数，所以Car类主构造函数的一部分必须能匹配父类的构造函数。  
Car类构造函数中的id和year两个成员变量源自父类Vehicle，所以需要使用override关键字修饰。  
此外，这两个类都重写了java.lang.Object的toString方法，所以需要使用override关键字修饰toString方法。




