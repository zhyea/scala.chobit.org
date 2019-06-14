---
title: package的使用
toc: true
date: 2018-05-05 18:16:34
tags:
categories: scala学习
---

这一节介绍一些关于package的内容。本来不想写关于package的内容的，因为一开始觉得没啥好写的。这次写因为发现了点儿可以写的内容。

## package定义

scala中package定义有如下几种方式：

### 在文件顶部定义。
```scala
package com.zhyea.robin

class MyApp
```
这种方式和java相同。  

### 多层级package定义
```scala
package com{
    package zhyea{
        package robin{
            class MyApp
        }
    }
}
```
这种定义方式比较繁琐，通常都不会采用。

### 串联式package定义
```scala
package com.zhyea{
    package robin{
        class MyApp
    }

    package tom{
        class MyApp
    }
}
```
这种方式算是上一种方式的优化版。  

有的时候我们需要定义几个结构比较简单的class，又不可以将之放在同一个package下，就可以采用这种方案。

### 组合式

定义package的时候还可以把第一种方式和第二或第三种方式组合起来使用：
```scala
package com.zhyea

package robin{
    class MyApp
}

package tom{
    class MyApp
}
```

## package特性

比较起Java来，scala的package定义有许多特别的地方：
1. scala的包定义不受源文件所在目录结构的限制；
1. 同一个包的定义是可以在不同的源文件中的；
1. 同一个源文件中是可以存在多个包的。

java的包声明和文件系统关联比较紧密，比如java的一个public类com.zhyea.Test，我们就可以判断出这个类的源文件是在类路径的com/zhyea路径下。

scala的包声明不受文件系统的限制，比如一个位于com/zhyea路径下的scala源文件，其顶端的包声明完全可以是“package org.chobit”。另外scala也并不要求源文件的名称要与类名相同。

根据前面说到的第三种和第四种包定义方式，一个文件中存在多个包是没有问题的。另外一个源文件中定义的多个包可以互相没有任何关系，比如下面这样的定义：
```scala
package com.zhyea{
  class MyApp
}

package org.chobit{
  class MyApp
}
```
这里定义的两个package不必一定要有相同的父级包。


## 相对路径和绝对路径

先来看一段代码：
```scala
package com.zhyea {

  package scala {

    class MyApp {
      val list = new scala.collection.mutable.ArrayBuffer[String] //会在此处报错，使用了相对路径
    }

  }

}
```
这段代码会在标明的位置报错。因为scala默认会在当前位置向上的相对路径中去找相关的类。在标明会报错的那一行的父级包正好是scala，所以实际上声明的list是这样的类型：
```scala
val list = new com.zhyea.scala.collection.mutable.ArrayBuffer[String]
```
编译器会从com.zhyea.scala中去找声明的类，找不到自然就会报错。

在编程的时候应当尽量避免出现问题。如果是在无法避免可以使用“\_root\_”声明通过绝对路径去寻找相关的类。如下：
```scala
package com.zhyea {

  package scala {

    class MyApp {
      val list = new _root_.scala.collection.mutable.ArrayBuffer[String] //通过绝对路径查找，不会报错
    }

  }

}
```
Scala 定义_root_包为用户可创建的包之外的包，也就是说任何顶层包都被当作是_root_包的成员。

## package可见性

关于scala可见性的内容[前面的一篇文](wiki/20180429/a11-accessModifier/)已经说了许多了。  
这里仅提一点：scala子包中的类可以访问父包中的类。

不多说，直接看个例子好了：
```scala
package com.zhyea


class Fruit(val name: String)

package male {

  class Worker(name: String) {
    def eat(fruit: Fruit) = println(s"$name is eating ${fruit.name}")
  }

}
```

## package对象  

package对象或者说package object是一种特殊的object。它的定义方式为：
> package object packageName{
    valueOrVariant
    methods
}

package对象的名称需要与package的名称一致，否则就没有意义。package下面的类和对象可以直接访问同名package对象中定义的值或方法。

看个例子：
```scala
package com.zhyea {

  package object student {
    val job: String = "Student"

    def duty: String = "Study"
  }

  package student {

    class Student(val name: String) {
      override def toString(): String = s"$name's job is $job, and his/her duty is ${duty}"
    }

  }

  object MyApp extends App {
    println(new student.Student("robin"))
  }

}
```
通过例子可以看出，scala通过package对象在package这一层对功能又做了一次聚合。通过package对象，scala可以解决一些功能增强及版本兼容相关的问题。