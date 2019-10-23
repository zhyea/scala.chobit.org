---
title: scala中的自适应做法
toc: true
date: 2018-05-01 07:21:13
tags:
categories: scala技巧
---

scala有一些默认做法，会让代码更简洁、更易读写，下面列出了这样几个特性：

1. 支持脚本。scala支持脚本，因此无须将所有的代码都放到类里。如果脚本可以满足需求，就将代码放到一个脚本里，无须再创建一个冗余的类。

1. return是可选的。如果没有写return关键字，方法调用会自动返回最后一个求值的表达式——如果它符合方法声明的返回值类型。

1. 分号“；”是可选的。不必在每个语句的后面都写上分号，这样会使代码更简洁。如果语句太长或者包含多行的话可以换行继续写，scala能够识别语句是否完整。通常使用分号的场景就是一行内有多条语句。

1. 类和方法默认是public的，不必显式使用public关键字。

1. Scala提供了轻量级的语法创建JavaBean——用更少的代码创建变量和final属性。 

1. Scala不会强制捕获一些不关心的异常，降低了代码规模，也避免了不恰当的异常处理。  


另外，默认情况下，scala会导入两个包和一个对象。导入顺序如下：
```scala
java.lang
scala
scala.Predef
```

两个包是：java.lang和scala，一个对象是scala.Predef。  

因为有这个默认导入的机制，使用这两个包中的类不需要使用全限定类名。比如使用String类时，就不需要使用java.lang.String，也不必显式导入。因为默认导入了scala包中的全部内容，所以可以很容易的使用scala中的类型。  

Predef对象（是对象不是类哦）包含了类型、隐式转换以及一些常用的方法。因为已经默认导入了，所以使用时无需导入也不需要任何前缀即可使用这些方法和转换。这些操作非常便捷，以至于在刚开始使用时会将之视为scala语言的一部分，实际上他们是scala程序库的一部分。  

Predef对象也为诸如scala.collection.immutable.Set和scala.collection. immutable.Map这样的东西提供了别名。比如，引用Set或Map，实际上引用的是他们在Predef中的定义，它们会依次转换为其在scala.collection.immutable包里的定义。
