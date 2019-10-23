---
title: 一元操作符
toc: false
date: 2018-06-11 22:06:59
tags: 操作符
categories: scala技巧
---


一元操作符是前置操作符，同“:”结尾的方法一样，也是以其后的实例为目标的。  

一元操作符包括+、-、！和~。  

在调用的时候一元操作符被转化为名为“unary_ + 操作符”的方法调用。看下下面的例子：

```scala
class Sample {
  def unary_+ = println("Called unary +")

  def unary_- = println("called unary -")

  def unary_! = println("called unary !")

  def unary_~ = println("called unary ~")
}

val sample = new Sample
+sample
-sample
!sample
~sample
```

Scala对方法名有规定，运算符不允许放在字母、数字这样的字符后面，除非这个运算符有下划线作为前缀。也就是说，一个方法不能被命名为jumpOver:()，只可以被命名为jumpOver_:()。

看下执行结果：

```text
Called unary +
called unary -
called unary !
called unary ~
```
