---
title: trait方法延迟绑定
toc: true
date: 2018-05-30 07:14:00
tags: trait
categories: scala学习
---


关于延迟绑定上一节已经提到过了。这次再专门说一下。  

延迟绑定简单来说就是先混入的trait的方法会后调用。

再来看一个例子：

```scala
abstract class Writer {
  def write(message: String): String
}

trait UpperWriter extends Writer {
  abstract override def write(message: String): String = super.write(message.toUpperCase)
}


trait FilterWriter extends Writer {
  abstract override def write(message: String): String = super.write(message.replace('o', '-'))
}


trait StringWriter extends Writer {
  def write(message: String): String = message
}

object MyTrait extends App {

  val myWriter1 = new StringWriter with UpperWriter with FilterWriter

  val myWriter2 = new StringWriter with FilterWriter with UpperWriter

  println(myWriter1.write("Hello World!"))

  println(myWriter2.write("Hello World!"))

}
```

在代码中定义了一个抽象类和三个trait。

其中抽象类Writer仅定义了一个抽象方法，并没有提供具体的实现。继承抽象类Writer的trait需要实现write方法。

UpperWriter的write方法实现了将传入的英文字符转为大写；

FilterWriter的write方法实现了将小写的“o”替换为“-”；

StringWriter则只是将传入的字符串原样返回。

看下执行结果：

```text
HELL- W-RLD!
HELLO WORLD!
```

验证了我们的说法：延迟绑定就是先混入的trait会后执行。

myWriter1的执行顺序：FilterWriter –> UpperWriter –> StringWriter；

myWriter2的执行顺序：UpperWriter –> FilterWriter –> StringWriter。

从trait的延迟绑定很容易会想到java的父类与子类的初始化顺序。又或者是java中的责任链模式。因此想想用java来实现这一点并不难：可以采用不同顺序的责任链，也可以是使用不同的继承顺序来实现。