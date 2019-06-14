---
title: 快速体验Scala - Scasite
toc: false
date: 2018-04-21 16:06:42
tags:
categories: scala学习
---

前面一节简略说了Scala开发环境的准备工作。如果还没有安装Scala的开发环境，也可以快速在[Scasite](https://scastie.scala-lang.org "Scasite")上快速体验一下。  

还是按传统，来一个HelloWorld：
```scala
object HelloWorld {  
      def main(args: Array[String]) = println("Hello World!")
}
```
这段代码中我们使用object关键字声明了一个结构：HelloWorld。之所以用“结构”这个词，是因为在没有上下文的情况下要描述清楚scala的object需要费些力气。不过可以用Java的一些概念来勉强描述下：scala的object相当于Java中的一个没有构造器，只有静态方法的final类。

再来看一下这个object里定义方法的形式：声明方法使用了def关键字，然后是方法名，方法名之后的括号中是参数列表，参数列表后一个等号紧跟着方法体。因为方法体只有一行，所以就没有用花括号括起来。参数列表中参数名和参数类型用冒号分隔，参数名在前。

打开[Scasite](https://scastie.scala-lang.org "Scasite")。将这段代码放在Scasite的编辑框里，点击Run按钮执行。可以看到在下方的Console中输出了Hello Word！。  
不过同时还出现了一行提示：
```txt
You don't need a main method (or extends Scastie) in Worksheet Mode  
```

说实话，我不太清楚Scasite的Worksheet Mode是什么意思。不过这提醒我了：Scala也是一种脚本语言。所以我们可以直接在Scasite的编辑框里输入下面的代码：
```scala
println("Hello World!")
```
点击Run按钮执行，结果与之前一样。

